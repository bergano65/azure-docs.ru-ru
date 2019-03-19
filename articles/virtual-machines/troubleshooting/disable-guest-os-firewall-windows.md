---
title: Отключение брандмауэра гостевой ОС на виртуальной машине Azure | Документация Майкрософт
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820031"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Отключение брандмауэра гостевой ОС на виртуальной машине Azure

Инструкции в этой статье относятся к ситуациям, в которых предполагается полная или частичная блокировка трафика виртуальной машины брандмауэром гостевой ОС. Такое может произойти, если в брандмауэре намеренно настроены дополнительные параметры, которые вызывают сбой подключения по протоколу RDP.

## <a name="solution"></a>Решение

Описанное решение позволит вам сосредоточиться на исправлении реальной проблемы, то есть на правильной настройке правил брандмауэра. Корпорация Майкрософт не рекомендует отключать брандмауэр Windows. Конфигурация правил брандмауэра зависит от требуемого уровня доступа к виртуальной машине.

### <a name="online-solutions"></a>Сетевые решения 

Если виртуальная машина подключена к сети и доступна для другой виртуальной машины в той же виртуальной сети, проблемы можно устранить с помощью второй виртуальной машины.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Решение 1. Пользовательские функции расширение скрипта или выполнить команду

Если у вас есть работающий агент Azure, можно применить [расширение пользовательских скриптов](../extensions/custom-script-windows.md) или функцию [Выполнить команду](../windows/run-command.md) (только для виртуальных машин Resource Manager), чтобы удаленно выполнить следующие скрипты.

> [!Note]
> * Если брандмауэр настроен локально, выполните следующий скрипт:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Если брандмауэр настроен через политику Active Directory, можно выполнить следующий скрипт для получения временного доступа. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Но сеанс удаленного подключения будет принудительно разорван, когда политика будет применена повторно. Постоянным решением этой проблемы является изменение политики, примененной к затронутому компьютеру.

#### <a name="mitigation-2-remote-powershell"></a>Решение 2. Удаленный сеанс PowerShell

1.  Подключитесь к любой виртуальной машине, которая находится в той же виртуальной сети, что и виртуальная машина, к которой не удается создать RDP-подключение.

2.  Откройте окно консоли PowerShell.

3.  Выполните следующие команды:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Если брандмауэр задается с помощью объекта групповой политики, этот метод может не работать, так как эта команда изменяет только записи в локальном реестре. Если настроена политика, она переопределит это изменение. 

#### <a name="mitigation-3-pstools-commands"></a>Решение 3. Команды PSTools

1.  На виртуальной машине для устранения неполадок скачайте средство [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Откройте экземпляр командной строки и создайте подключение к виртуальной машине по выделенному IP-адресу.

3.  Выполните следующие команды:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Решение 4. Удаленный реестр 

Ниже приведены инструкции по использованию [удаленного реестра](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  На виртуальной машине для устранения неполадок запустите редактор реестра и выберите **Файл** > **Подключить сетевой реестр**.

2.  Откройте ветвь  *ИМЯ_ЦЕЛЕВОГО_КОМПЬЮТЕРА*\SYSTEM и укажите следующие значения:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Перезапустите службу. Это нельзя сделать с помощью удаленного реестра, поэтому используйте консоль удаления службы.

4.  Откройте экземпляр  **Services.msc**.

5.  Щелкните элемент **Службы (локальные)**.

6.  Выберите **Connect to another computer** (Подключение к другому компьютеру).

7.  Введите  **частный IP-адрес**  проблемной виртуальной машины.

8.  Перезапустите политику локального брандмауэра.

9.  Повторите попытку подключения к виртуальной машине по протоколу RDP с локального компьютера.

### <a name="offline-solutions"></a>Автономные решения 

Если вы не можете получить доступ к виртуальной машине, расширение пользовательского скрипта не сработает, и проблему нужно будет решать в автономном режиме, то есть прямо через системный диск. Для этого выполните следующие действия:

1.  [Подключите системный диск к виртуальной машине восстановления](troubleshoot-recovery-disks-portal-windows.md).

2.  Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.

3.  Убедитесь, что в консоли управления дисками подключенный диск имеет состояние подключенного. Запишите или запомните букву диска, которая присвоена подключенному системному диску.

4.  Прежде чем вносить любые изменения, создайте копию папки \windows\system32\config на случай, если потребуется откат изменений.

5.  На виртуальной машине восстановления запустите редактор реестра (regedit.exe). 

6.  В нашем примере мы подключаем кусты BROKENSYSTEM и BROKENSOFTWARE.

7.  Выберите раздел HKEY_LOCAL_MACHINE, а затем откройте пункты меню "Файл" > "Загрузить куст".

8.  Найдите файл \windows\system32\config\SYSTEM на подключенном ранее системном диске.

9.  Откройте экземпляр PowerShell с повышенными правами и выполните следующие команды:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Отсоедините системный диск и повторно создайте виртуальную машину](troubleshoot-recovery-disks-portal-windows.md).

11. Убедитесь, что проблема устранена.
