---
title: Сбой удаленного подключения к виртуальным машинам Azure из-за отключенного DHCP | Документация Майкрософт
description: Способы устранения неполадок с подключением удаленного рабочего стола в Microsoft Azure, связанных с отключением службы DHCP-клиента | Документация Майкрософт
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
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 5842c5edd0402d61f564ab15e34e8f69c0e718d7
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213456"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Не удается подключиться по протоколу удаленного рабочего стола к виртуальным машинам Azure из-за того, что отключена служба DHCP-клиента

В этой статье описывается проблема, при которой не удается установить подключение удаленного рабочего стола к виртуальным машинам Azure под управлением Windows после отключения службы DHCP-клиента на виртуальной машине.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Проблемы

Невозможно создать RDP-подключение к виртуальной машине в Azure, так как на ней отключена служба DHCP-клиента. При проверке снимка экрана в разделе [Диагностика загрузки](../troubleshooting/boot-diagnostics.md) на портале Azure вы увидите, что виртуальная машина загружается нормально и ожидает учетных данных на экране входа. Вы удаленно просматриваете журналы событий в виртуальной машине c помощью средства "Просмотр событий". Вы увидите, что служба DHCP-клиента отключена или не запускается. Ниже приведен пример журнала:

**Log Name**: системный; </br>
**Source**: Service Control Manager </br>
**Date**: 12/16/2015 11:19:36 AM </br>
**Event ID**: 7022 </br>
**Task Category**: Нет </br>
**Level**: Ошибка </br>
**Keywords**: Классический</br>
**User**: Недоступно </br>
**Computer**: myvm.cosotos.com</br>
**Описание** The DHCP Client service hung on starting.</br>

Для виртуальных машин Resource Manager можно получить журналы для событий 7022 через последовательную консоль доступа, выполнив следующую команду:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Для классических виртуальных машин придется переключиться в автономный режим и собрать журналы вручную.

## <a name="cause"></a>Причина:

Служба DHCP-клиента не работает на виртуальной машине.

> [!NOTE]
> Эта статья относится только к службе DHCP-клиента, но не DHCP-сервера.

## <a name="solution"></a>Решение

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

Для решения этой проблемы включите DHCP через последовательную консоль или [сбросьте сетевой интерфейс](reset-network-interface.md) для виртуальной машины.

### <a name="use-serial-control"></a>Использование последовательной консоли

1. Подключитесь к [последовательной консоли и откройте экземпляр командной строки](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
Иначе будет отображено такое сообщение об ошибке несоответствия встроенного ПО: "Невозможно продолжить сброс параметров к значениям по умолчанию из-за несоответствия версий встроенного ПО". Если последовательная консоль на нужной виртуальной машине не включена, ознакомьтесь со сведениями о том, как [сбросить сетевой интерфейс](reset-network-interface.md).
2. Проверьте, отключен ли протокол DHCP в сетевом интерфейсе:

        sc query DHCP
3. Если служба DHCP остановлена, попробуйте запустить эту службу

        sc start DHCP

4. Запросите службу еще раз, чтобы убедиться, что она успешно запущена.

        sc query DHCP

    Попробуйте подключиться к виртуальной машине и убедитесь, что проблема устранена.
5. Если служба не запускается, используйте одно из следующих решений в зависимости от полученного сообщения об ошибке:

    | Ошибка  |  Решение |
    |---|---|
    | 5 — ACCESS DENIED  | Перейдите к разделу [Служба DHCP-клиента остановлена из-за ошибки отказа в доступе](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 — ERROR_SERVICE_REQUEST_TIMEOUT   | Перейдите к разделу [Происходит сбой службы DHCP-клиента или она зависает](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 — ERROR_SERVICE_DISABLED  | Перейдите к разделу [Служба DHCP-клиента отключена](#dhcp-client-service-is-disabled).  |
    | 1059 — ERROR_CIRCULAR_DEPENDENCY  |[Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.   |
    | 1067 — ERROR_PROCESS_ABORTED |Перейдите к разделу [Происходит сбой службы DHCP-клиента или она зависает](#dhcp-client-service-crashes-or-hangs).   |
    |1068 — ERROR_SERVICE_DEPENDENCY_FAIL   | [Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.  |
    |1069 — ERROR_SERVICE_LOGON_FAILED   |  Перейдите к разделу [Происходит сбой службы DHCP-клиента из-за ошибки входа в систему](#dhcp-client-service-fails-because-of-logon-failure). |
    | 1070 — ERROR_SERVICE_START_HANG  | Перейдите к разделу [Происходит сбой службы DHCP-клиента или она зависает](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 — ERROR_SERVICE_NEVER_STARTED  | Перейдите к разделу [Служба DHCP-клиента отключена](#dhcp-client-service-is-disabled).  |
    |1079 — ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.  |
    |1053 | [Свяжитесь со службой поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы быстро решить проблему.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>Служба DHCP-клиента остановлена из-за ошибки отказа в доступе

1. Подключитесь к [последовательной консоли](serial-console-windows.md) и откройте экземпляр PowerShell.
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
4. Воспроизведите проблему, запустив службу, из-за которой появляется сообщение об **отказе в доступе**:

   ```
   sc start DHCP
   ```

   Когда запуск завершится ошибкой, остановите трассировку Process Monitor:

   ```
   procmon /Terminate
   ```
5. Соберите файл **c:\temp\ProcMonTrace.PML**:

    1. [Подключение диска данных к виртуальной машине](../windows/attach-managed-disk-portal.md
).
    2. С помощью последовательной консоли можно скопировать файл на новый диск. Например, `copy C:\temp\ProcMonTrace.PML F:\`. В этой команде F является буквой подключенного диска данных. Замените эту букву правильным значением для вашей системы.
    3. Отключите диск данных и подключите его к работающей виртуальной машине, на которой установлено средство ubstakke Process Monitor.

6. Откройте **ProcMonTrace.PML** с помощью Process Monitor на рабочей виртуальной машине. Затем отфильтруйте элементы, для результата которых задано значение  **В доступе отказано**, как показано на указанном ниже снимке экрана:

    ![Фильтрация по результатам в Process Monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Исправьте разделы реестра, папки или файлы, которые включены в выходные данные. Обычно эта проблема возникает, если у учетной записи, использованной для входа в систему, нет разрешения ACL на доступ к этим объектам. Чтобы выяснить правильное разрешение в ACL для учетной записи, используемой для входа в систему, просмотрите данные на исправной виртуальной машине.

#### <a name="dhcp-client-service-is-disabled"></a>Служба DHCP-клиента отключена

1. Восстановите параметр запуска службы по умолчанию:

   ```
   sc config DHCP start= auto
   ```

2. Запустите службу:

   ```
   sc start DHCP
   ```

3. Запросите состояние службы еще раз и убедитесь, что она запущена:

   ```
   sc query DHCP
   ```

4. Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Происходит сбой службы DHCP-клиента из-за ошибки входа в систему

1. Поскольку эта проблема возникает при изменении стартовой учетной записи для службы, восстановите для нее стандартную учетную запись:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Запустите службу:

        sc start DHCP
3. Попробуйте подключиться к виртуальной машине с помощью удаленного рабочего стола.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>Происходит сбой службы DHCP-клиента или она зависает
1. Если служба зависла в состоянии **Запуск** или**Остановка**, попробуйте остановить эту службу:

        sc stop DHCP
2. Изолируйте службу в собственном контейнере svchost.

        sc config DHCP type= own
3. Запустите службу:

        sc start DHCP
4. Если служба по-прежнему не запускается, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления. Убедитесь, что в консоли "Управление дисками" для подключенного диска отображается состояние **Подключен**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3.  Откройте командную строку с повышенными привилегиями (**Запуск от имени администратора**). Затем выполните следующий сценарий. В этом скрипте предполагается, что подключенному диску ОС присвоена буква **F**. Замените эту букву правильным значением для вашей виртуальной машины.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Отключение диска операционной системы и повторное создание виртуальной машины](../windows/troubleshoot-recovery-disks-portal.md). Затем проверьте, устранена ли проблема.

## <a name="next-steps"></a>Дополнительная информация

Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет устранить проблему.


