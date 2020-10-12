---
title: Службы удаленных рабочих столов не запускаются на виртуальной машине Azure | Документация Майкрософт
description: Сведения об устранении неполадок служб удаленных рабочих столов при подключении к виртуальной машине | Документация Майкрософт
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508888"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Службы удаленных рабочих столов не запускаются на виртуальной машине Azure

В этой статье описывается устранение неполадок подключения к виртуальной машине Azure, когда службы удаленных рабочих столов (TermService) не запускаются или их запуск завершается ошибкой.


## <a name="symptoms"></a>Симптомы

При попытке подключиться к виртуальной машине возникают следующие сценарии:

- Снимок экрана виртуальной машины: операционная система полностью загружена и ожидает учетные данные.

    ![Снимок экрана с состоянием виртуальной машины](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Вы удаленно просматриваете журналы событий в виртуальной машине c помощью средства "Просмотр событий". Вы увидите, что службы удаленных рабочих столов (TermService) не запускаются или происходит сбой запуска. Ниже приведен пример журнала:

    **Имя журнала**: System </br>
    **Источник**: диспетчер управления службами </br>
    **Дата**: 16.12.2017 11:19:36</br>
    **Идентификатор события**: 7022</br>
    **Категория задачи**: Отсутствует</br>
    **Уровень**: ошибка</br>
    **Ключевые слова**: классические</br>
    **Пользователь**: н/д</br>
    **Компьютер**: vm.contoso.com</br>
    **Описание**: Служба "Службы удаленных рабочих столов" зависла при запуске. 

    Кроме того, доступ к этим ошибкам можно получить через консоль последовательного доступа с помощью следующего запроса: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Причина
 
Эта проблема возникает, потому что службы удаленных рабочих столов не запущены на виртуальной машине. Причина может быть в следующем: 

- Служба TermService **отключена**. 
- Служба TermService работает со сбоем или не отвечает. 
- TermService не запускается из-за неверной конфигурации.

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
    |5 — ACCESS DENIED |Ознакомьтесь с разделом [Служба TermService остановлена из-за ошибки отказа в доступе](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 — ERROR_SERVICE_REQUEST_TIMEOUT  |См. раздел [Служба TermService отключена](#termservice-service-is-disabled).  |  
    |1058 — ERROR_SERVICE_DISABLED  |Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termservice-service-crashes-or-hangs).  |
    |1059 — ERROR_CIRCULAR_DEPENDENCY |[Обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , чтобы быстро устранить проблему.|
    |1067 — ERROR_PROCESS_ABORTED  |Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termservice-service-crashes-or-hangs).  |
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL|[Обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , чтобы быстро устранить проблему.|
    |1069 — ERROR_SERVICE_LOGON_FAILED  |Ознакомьтесь с разделом [Происходит сбой службы TermService из-за ошибки входа в систему](#termservice-service-fails-because-of-logon-failure). |
    |1070 — ERROR_SERVICE_START_HANG   | Ознакомьтесь с разделом [Происходит сбой службы TermService сбоя или она зависает](#termservice-service-crashes-or-hangs). |
    |1077 — ERROR_SERVICE_NEVER_STARTED   | См. раздел [Служба TermService отключена](#termservice-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , чтобы быстро устранить проблему. |
    |1753   |[Обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , чтобы быстро устранить проблему.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Служба TermService остановлена из-за отказа в доступе

1. Подключитесь к [последовательной консоли](serial-console-windows.md) и откройте экземпляр PowerShell.
2. Загрузите средство "монитор процессов", выполнив следующий скрипт:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Теперь запустите трассировку **Procmon** :

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

5. Собирайте файл **к:\темп\прокмонтраце.ПМЛ**:

    1. [Подключение диска данных к виртуальной машине](../windows/attach-managed-disk-portal.md
).
    2. С помощью последовательной консоли можно скопировать файл на новый диск. Например, `copy C:\temp\ProcMonTrace.PML F:\`. В этой команде F является буквой подключенного диска данных.
    3. Отключите диск данных и подключите его на работающей виртуальной машине, на которой установлено средство ubstakke Process Monitor.

6. Откройте **ProcMonTrace.PML** с помощью Process Monitor рабочей виртуальной машины. Затем для фильтра по **результату будет отказано в доступе**, как показано на следующем снимке экрана:

    ![Фильтр по результату в мониторе обработки](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
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

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Запустите службу:

    ```console
    sc start TermService
    ```

3. Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.

#### <a name="termservice-service-crashes-or-hangs"></a>Происходит сбой службы TermService сбоя или она зависает
1. Если служба зависла в состоянии **Запуск** или**Остановка**, попробуйте остановить службу: 

    ```console
    sc stop TermService
    ```

2. Изолируйте службу в собственном контейнере svchost.

    ```console
    sc config TermService type= own
    ```

3. Запустите службу:

    ```console
    sc start TermService
    ```

4. Если службу по-прежнему не удается запустить, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Подключите диск операционной системы к виртуальной машине восстановления](./troubleshoot-recovery-disks-portal-windows.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления. Убедитесь, что в консоли "Управление дисками" для подключенного диска отображается состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3. Откройте экземпляр командной строки с повышенными привилегиями (**Запуск от имени администратора**). Затем выполните следующий сценарий. Предполагается, что буква диска, назначенная подключенному диску ОС, — **F**. Замените его соответствующим значением в виртуальной машине. 

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

4. [Отключение диска операционной системы и повторное создание виртуальной машины](./troubleshoot-recovery-disks-portal-windows.md). Затем проверьте, устранена ли проблема.

## <a name="need-help-contact-support"></a>Требуется помощь? Обращение в службу поддержки

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
