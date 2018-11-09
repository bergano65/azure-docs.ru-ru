---
title: Службы удаленных рабочих столов не запускаются на виртуальной машине Azure | Документация Майкрософт
description: Узнайте, как устранять неполадки служб удаленных рабочих столов при подключении к виртуальной машине.
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
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238868"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Службы удаленных рабочих столов не запускаются на виртуальной машине Azure

В этой статье описывается устранение неполадок подключения к виртуальной машине Azure, когда службы удаленных рабочих столов (TermService) не запускаются или их запуск завершается ошибкой.

>[!NOTE]
>В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания Resource Manager. Для новых развертываний рекомендуется использовать эту модель развертывания вместо классической.

## <a name="symptoms"></a>Проблемы

При попытке подключиться к виртуальной машине возникают следующие сценарии:

- Снимок экрана виртуальной машины: операционная система полностью загружена и ожидает учетные данные.

    ![Снимок экрана с состоянием виртуальной машины](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- При удаленном просмотре журналов событий на виртуальной машине с помощью средства "Просмотр событий" видно, что службы удаленных рабочих столов (TermServ) не запускаются или их запуск завершается ошибкой. Ниже приведен пример журнала:

    **Имя журнала**: Система </br>
    **Источник**: Service Control Manager </br>
    **Дата**: 16.12.2017 11:19:36</br>
    **Код события**: 7022</br>
    **Категория задачи	**: Отсутствует</br>
    **Уровень**: Ошибка</br>
    **Ключевые слова**: Классический</br>
    **Пользователь**: Н/Д</br>
    **Компьютер**: vm.contoso.com</br>
    **Описание**: Служба "Службы удаленных рабочих столов" зависла при запуске. 

    Также доступ к этим ошибкам можно получить через консоль последовательного доступа, используя следующий запрос: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Причина:
 
Эта проблема возникает, потому что службы удаленных рабочих столов не запущены на виртуальной машине. Причина может быть в следующем: 

- Служба TermService **отключена**. 
- произошло аварийное завершение или зависание службы TermService. 

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, воспользуйтесь последовательной консолью или [восстановите виртуальную машину в автономном режиме](#repair-the-vm-offline), присоединив диск ОС виртуальной машины к виртуальной машине для восстановления.

### <a name="use-serial-console"></a>Использование последовательной консоли

1. Откройте [последовательную консоль](serial-console-windows.md), выбрав **Поддержка и устранение неполадок** > **Последовательная консоль**. Если эта функция включена на виртуальной машине, вы сможете успешно подключиться к виртуальной машине.

2. Создайте канал для экземпляра командной строки. Введите **CMD**, чтобы запустить канал и получить имя канала.

3. Переключитесь на канал, на котором запущен экземпляр CMD. В нашем случае это должен быть канал 1.

   ```
   ch -si 1
   ```

4. Нажмите клавишу **ВВОД** еще раз и введите допустимое имя пользователя и пароль (локальный или доменный идентификатор) для виртуальной машины.

5. Запросите состояние службы TermService.

   ```
   sc query TermService
   ```

6. Если отображается состояние службы **остановлена**, попытайтесь запустить службу.

    ```
    sc start TermService
     ``` 

7. Запросите службу еще раз, чтобы убедиться, что она успешно запущена.

   ```
   sc query TermService
   ```
    Если служба не запускается, выполните поиск решения на основе полученного сообщения об ошибке:

    |  Ошибка |  Предложение |
    |---|---|
    |5 — ACCESS DENIED |См. раздел [Служба TermService остановлена из-за ошибки отказа в доступе](#termService-service-is-stopped-because-of-access-denied-error). |
    |1058 — ERROR_SERVICE_DISABLED  |См. раздел [Служба TermService отключена](#termService-service-is-disabled).  |
    |1059 — ERROR_CIRCULAR_DEPENDENCY |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.|
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL|[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.|
    |1069 — ERROR_SERVICE_LOGON_FAILED  |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.    |
    |1070 — ERROR_SERVICE_START_HANG   | [Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.  |
    |1077 — ERROR_SERVICE_NEVER_STARTED   | См. раздел [Служба TermService отключена](#termService-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему. |
    |1753   |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>Служба TermService остановлена из-за ошибки отказа в доступе

1. Подключитесь к [последовательной консоли](serial-console-windows.md#) и откройте экземпляр PowerShell.
2. Скачайте средство Process Monitor, выполнив следующий скрипт:

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. Далее запустите трассировку procmon:

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. Воспроизведите проблему, запустив службу, которая выдает ошибку отказа в доступе: 

        sc start TermService 
        
    Когда запуск завершится ошибкой, остановите трассировку Process Monitor:

        procmon /Terminate 
5. Найдите файл  **c:\temp\ProcMonTrace.PML**, откройте его с помощью procmon и отфильтруйте с помощью запроса  **Result is ACCESS DENIED**, как показано на снимке экрана ниже:

    ![Фильтрация по результатам в Process Monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Исправьте разделы реестра, папки или файлы, которые включены в выходные данные. Обычна эта проблема возникает из-за того, что у учетной записи, использованной для входа в систему, нет разрешения в списке управления доступом на доступ к этим объектам. Чтобы узнать правильное разрешение в списке управления доступом для учетной записи, используемой для входа в систему, просмотрите данные на исправной виртуальной машине. 

#### <a name="termservice-service-is-disabled"></a>Служба TermService отключена

1.  Восстановите параметр запуска службы по умолчанию:

        sc config TermService start= demand 
        
2.  Запустите службу:

        sc start TermService 
3.  Выполните запрос состояния еще раз, чтобы убедиться, что служба запущена: sc query TermService 
4.  Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.


### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления. Убедитесь, что в консоли "Управление дисками" для подключенного диска отображается состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3.  Отройте командную строку с повышенными привилегиями (**Запуск от имени администратора**) и выполните приведенный ниже скрипт. Мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением на своей виртуальной машине. 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [Отключите диск ОС и повторно создайте виртуальную машину](../windows/troubleshoot-recovery-disks-portal.md), а затем проверьте, устранена ли проблема.

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
