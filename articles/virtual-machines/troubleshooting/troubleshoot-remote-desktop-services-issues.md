---
title: Службы удаленных рабочих столов не запускаются на виртуальной машине Azure | Документация Майкрософт
description: Сведения об устранении неполадок служб удаленных рабочих столов при подключении к виртуальной машине | Документация Майкрософт
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 904387def0fd8842f196e80cfcf72d9dd1639458
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957713"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Службы удаленных рабочих столов не запускаются на виртуальной машине Azure

В этой статье описывается устранение неполадок подключения к виртуальной машине Azure, когда службы удаленных рабочих столов (TermService) не запускаются или их запуск завершается ошибкой.

> [!NOTE]  
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания Resource Manager. Для новых развертываний рекомендуется использовать эту модель развертывания вместо классической.

## <a name="symptoms"></a>Проблемы

При попытке подключиться к виртуальной машине возникают следующие сценарии:

- Снимок экрана виртуальной машины: операционная система полностью загружена и ожидает учетные данные.

    ![Снимок экрана с состоянием виртуальной машины](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Вы удаленно просматриваете журналы событий в виртуальной машине c помощью средства "Просмотр событий". Вы увидите, что службы удаленных рабочих столов (TermService) не запускаются или происходит сбой запуска. Ниже приведен пример журнала:

    **Имя журнала**: Система </br>
    **Источник**: Service Control Manager </br>
    **Дата**: 16.12.2017 11:19:36</br>
    **Код события**: 7022</br>
    **Категория задачи**: Отсутствует</br>
    **Уровень**: Ошибка</br>
    **Ключевые слова**: Классический</br>
    **Пользователь**: Н/Д</br>
    **Компьютер**: vm.contoso.com</br>
    **Описание**: Служба "Службы удаленных рабочих столов" зависла при запуске. 

    Кроме того, доступ к этим ошибкам можно получить через консоль последовательного доступа с помощью следующего запроса: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Причина:
 
Эта проблема возникает, потому что службы удаленных рабочих столов не запущены на виртуальной машине. Причина может быть в следующем: 

- Служба TermService **отключена**. 
- произошло аварийное завершение или зависание службы TermService. 
- Служба TermService не запускается из-за неверной конфигурации.

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, используйте последовательную консоль. Вы также можете [восстановить виртуальную машину в автономном режиме](#repair-the-vm-offline) при подключении диска ОС к виртуальной машине восстановления.

### <a name="use-serial-console"></a>Использование последовательной консоли

1. Откройте [последовательную консоль](serial-console-windows.md), выбрав **Поддержка и устранение неполадок** > **Последовательная консоль**. Если эта функция включена на виртуальной машине, вы сможете успешно подключиться к виртуальной машине.

2. Создайте канал для экземпляра командной строки. Введите **CMD**, чтобы запустить канал и получить имя канала.

3. Переключитесь на канал, который запускает экземпляр CMD. В нашем случае это должен быть канал 1:

   ```
   ch -si 1
   ```

4. Еще раз нажмите клавишу **ВВОД** и введите допустимое имя пользователя и пароль, локальный или доменный идентификатор для виртуальной машины.

5. Запросите состояние службы TermService:

   ```
   sc query TermService
   ```

6. Если отображается состояние службы **Остановлена**, попытайтесь запустить службу:

    ```
    sc start TermService
     ``` 

7. Запросите службу еще раз, чтобы убедиться, что она успешно запущена:

   ```
   sc query TermService
   ```
8. Если служба не запускается, выполните поиск решения на основе полученного сообщения об ошибке:

    |  Ошибка |  Предложение |
    |---|---|
    |5 — ACCESS DENIED |Ознакомьтесь с разделом [Служба TermService остановлена из-за ошибки отказа в доступе](#termService-service-is-stopped-because-of-an-access-denied-problem). |   |1053 — ERROR_SERVICE_REQUEST_TIMEOUT  |Ознакомьтесь с разделом [Служба TermService отключена](#termService-service-is-disabled).  |  
    |1058 — ERROR_SERVICE_DISABLED  |Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termService-service-crashes-or-hangs).  |
    |1059 — ERROR_CIRCULAR_DEPENDENCY |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.|
    |1067 — ERROR_PROCESS_ABORTED  |Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termService-service-crashes-or-hangs).  |
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL|[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.|
    |1069 — ERROR_SERVICE_LOGON_FAILED  |Ознакомьтесь с разделом [Происходит сбой службы TermService из-за ошибки входа в систему](#termService-service-fails-because-of-logon-failure). |
    |1070 — ERROR_SERVICE_START_HANG   | Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termService-service-crashes-or-hangs). |
    |1077 — ERROR_SERVICE_NEVER_STARTED   | Ознакомьтесь с разделом [Служба TermService отключена](#termService-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему. |
    |1753   |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.   |   |5 — ACCESS DENIED |Ознакомьтесь с разделом [Служба TermService остановлена из-за ошибки отказа в доступе](#termService-service-is-stopped-because-of-an-access-denied-error). |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Служба TermService остановлена из-за отказа в доступе

1. Подключитесь к [последовательной консоли](serial-console-windows.md#) и откройте экземпляр PowerShell.
2. Скачайте средство Process Monitor, выполнив следующий скрипт:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Далее запустите трассировку **procmon**:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. Воспроизведите проблему, запустив службу, которая выдает ошибку **отказа в доступе**: 

   ```
   sc start TermService 
   ```

   Когда запуск завершится ошибкой, остановите трассировку Process Monitor:

   ```   
   procmon /Terminate 
   ```

5. Соберите файл  **c:\temp\ProcMonTrace.PML**:

    1. [Подключение диска данных к виртуальной машине](../windows/attach-managed-disk-portal.md
).
    2. С помощью последовательной консоли можно скопировать файл на новый диск. Например, `copy C:\temp\ProcMonTrace.PML F:\`. В этой команде F является буквой подключенного диска данных.
    3. Отключите диск данных и подключите его на работающей виртуальной машине, на которой установлено средство ubstakke Process Monitor.

6. Откройте **ProcMonTrace.PML** с помощью Process Monitor рабочей виртуальной машины. Затем отфильтруйте элементы, для результата которых задано значение  **В доступе отказано**, как показано на указанном ниже снимке экрана:

    ![Фильтрация по результатам в Process Monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Исправьте разделы реестра, папки или файлы, которые включены в выходные данные. Обычно эта проблема возникает, если у учетной записи, использованной для входа в систему, нет разрешения ACL на доступ к этим объектам. Чтобы узнать правильное разрешение в ACL для учетной записи, используемой для входа в систему, просмотрите данные на исправной виртуальной машине. 

#### <a name="termservice-service-is-disabled"></a>Служба TermService отключена

1. Восстановите параметр запуска службы по умолчанию:

   ```
   sc config TermService start= demand 
   ```

2. Запустите службу:

   ```
   sc start TermService
   ```

3. Выполните запрос состояния еще раз, чтобы убедиться, что служба запущена:

   ```
   sc query TermService 
   ```

4. Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Сбой службы TermService из-за ошибки входа в систему

1. Эта проблема возникает, если запускаемая учетная запись этой службы была изменена. Изменить ее на учетную запись по умолчанию: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Запустите службу:

        sc start TermService
3. Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.

#### <a name="termservice-service-crashes-or-hangs"></a>Происходит сбой службы TermService сбоя или она зависает
1. Если служба зависла в состоянии **Запуск** или**Остановка**, попробуйте остановить службу: 

        sc stop TermService
2. Изолируйте службу в собственном контейнере svchost.

        sc config TermService type= own
3. Запустите службу:

        sc start TermService
4. Если службу по-прежнему не удается запустить, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления. Убедитесь, что в консоли "Управление дисками" для подключенного диска отображается состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3.  Откройте командную строку с повышенными привилегиями (**Запуск от имени администратора**). Затем выполните следующий сценарий. Мы предполагаем, подключенному диску ОС присвоена буква **F**. Замените ее соответствующим значением в вашей виртуальной машине. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Отключение диска операционной системы и повторное создание виртуальной машины](../windows/troubleshoot-recovery-disks-portal.md). Затем проверьте, устранена ли проблема.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
