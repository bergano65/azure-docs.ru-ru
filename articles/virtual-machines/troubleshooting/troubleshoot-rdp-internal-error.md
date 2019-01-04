---
title: Внутренняя ошибка при попытке подключения к Виртуальным машинам Azure по протоколу удаленного рабочего стола | Документация Майкрософт
description: Узнайте, как устранить внутренние ошибки RDP в Microsoft Azure. | Документация Майкрософт
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
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: dd75d5a3186bbb6ba82e2deb83a7e8429e32a3f2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134528"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Внутренняя ошибка при попытке подключения к виртуальной Машине Azure через удаленный рабочий стол

В этой статье описаны ошибки, которые могут возникнуть при подключении к виртуальной машине в Microsoft Azure.
> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания c помощью Resource Manager. Для новых развертываний рекомендуется использовать эту модель вместо классической.

## <a name="symptoms"></a>Проблемы

Не удается подключиться к виртуальной машине Azure с помощью протокола удаленного рабочего стола (RDP). Подключение зависает на разделе "Configuring Remote" (Настройка удаленного рабочего стола), или появляется следующее сообщение об ошибке:

- внутренняя ошибка RDP;
- произошла внутренняя ошибка;
- не удается подключиться к удаленному компьютеру с этого компьютера. Повторите попытку подключения. Если проблема не исчезнет, ​​обратитесь к владельцу удаленного компьютера или к администратору сети.


## <a name="cause"></a>Причина:

Эта проблема может возникать из-за следующих причин:

- локальные ключи шифрования RSA недоступны;
- протокол TLS отключен;
- сертификат поврежден или истек срок его действия.

## <a name="solution"></a>Решение

Прежде чем выполнять какие-либо действия, сделайте моментальный снимок диска ОС затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](../windows/snapshot-copy-managed-disk.md).

Чтобы устранить эту проблему, воспользуйтесь последовательной консолью или [восстановите виртуальную машину в автономном режиме](#repair-the-vm-offline), присоединив диск ОС виртуальной машины к виртуальной машине для восстановления.


### <a name="use-serial-control"></a>Использование последовательной консоли

Подключитесь к [последовательной консоли и откройте экземпляр PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Если последовательную консоль не включено на виртуальной машине, перейдите к разделу [repair the VM offline](#repair-the-vm-offline)(Автономное восстановление виртуальной машины).

#### <a name="step-1-check-the-rdp-port"></a>Шаг 1. Проверка порта RDP

1. В экземпляре PowerShell используйте [netstat](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) для проверки того, используется ли порт 8080 другими приложениями.

        Netstat -anob |more
2. Если Termservice.exe использует порт 8080, перейдите к шагу 2. Если другая служба или приложение, отличное от Termservice.exe, использует порт 8080, выполните следующие действия.

    1. Остановите службу для приложения, которое использует службу 3389.

        Stop-Service -Name <ServiceName>

    2. Запустите службу терминалов.

        Start-Service -Name Termservice

2. Если не удается остановить приложение, или этот метод не подходит, измените порт для RDP.

    1. Измените порт.

        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice Start-Service -Name Termservice

    2. Настройте брандмауэр в соответствии с новым портом.

        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <НОВЫЙ_ПОРТ (десятичное число)>

    3. [Обновите группу безопасности сети для нового порта](../../virtual-network/security-overview.md) на порте RDP портала Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Шаг 2. Установка правильных разрешений на самозаверяющем сертификате RDP

1.  Чтобы обновить самозаверяющий сертификат RDP, по очереди выполните следующие команды в экземпляре PowerShell.

        Import-Module PKI Set-Location Cert:\LocalMachine $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Если не удается обновить сертификат с помощью этого метода, попробуйте обновить самозаверяющий сертификат RDP удаленно.

    1. С работающей виртуальной машины с подключением к той виртуальной машине, на которой возникли проблемы, введите **mmc** в окне**Запуск**, чтобы открыть консоль управления (MMC).
    2. В меню **Файл**выберите **Add/Remove Snap-in** (Добавить или удалить оснастку), выберите **Сертификаты**, а затем выберите **Добавить**.
    3. Выберите **Computer accounts** (учетные записи компьютера), выберите **Another Computer** (другой компьютер), а затем добавьте IP-адрес проблемной виртуальной машины.
    4. Перейдите в папку **Remote Desktop\Certificates** (Удаленный рабочий стол или сертификаты), щелкните правой кнопкой мыши сертификат и затем щелкните **Удалить**.
    5. В экземпляре PowerShell из последовательной консоли перезапустите службу настройки удаленного рабочего стола.

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Сбросьте разрешение для папки MachineKeys

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt Restart-Service TermService -Force

4. Перезапустите виртуальную машину, а затем повторите попытку подключения к виртуальной машине с удаленного рабочего стола. Если ошибку не удалось устранить, перейдите к следующему шагу.

Шаг 3. Шаг 3. Включение всех поддерживаемых версий протокола TLS

Клиент RDP использует TLS 1.0 в качестве протокола по умолчанию. Тем не менее его можно изменить на TLS 1.1, который является новым стандартом. Если протокол TLS 1.1 отключен на виртуальной машине, произойдет сбой подключения.
1.  В экземпляре CMD включите протокол TLS.

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Чтобы предотвратить перезапись изменений политики AD, временно остановите обновление групповой политики.

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Чтобы изменения вступили в силу, перезапустите виртуальную машину. Если проблема устранена, выполните следующую команду, чтобы снова включить групповую политику.

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Если изменение отменено, это означает, что в домене вашей компании уже имеется политика Active Directory. Чтобы впредь избежать этой проблемы, необходимо изменить эту политику.

### <a name="repair-the-vm-offline"></a>Автономное восстановление виртуальной машины

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Подключите диск ОС к виртуальной машине восстановления.

1. [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью портала Azure](../windows/troubleshoot-recovery-disks-portal.md).
2. После подключения диска ОС к виртуальной машине восстановления убедитесь, что в консоли управления дисками он помечен как **В сети**. Запишите или запомните букву диска, которая присвоена подключенному диску ОС.
3. Установите подключение с помощью удаленного рабочего стола к виртуальной машине, используемой для восстановления.

#### <a name="enable-dump-log-and-serial-console"></a>Включение журнала дампа и последовательной консоли

Чтобы включить журнал дампа и последовательную консоль, выполните следующий сценарий.

1. Откройте сеанс командной строки с повышенными привилегиями (**запуск от имени администратора**).
2. Выполните следующий скрипт:

    В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением для своей виртуальной машины.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Сброс разрешения для папки MachineKeys

1. Откройте сеанс командной строки с повышенными привилегиями (**запуск от имени администратора**).
2. Выполните следующий сценарий. В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением для своей виртуальной машины.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Шаг 3. Включение всех поддерживаемых версий протокола TLS

1.  Отройте сеанс командной строки с повышенными привилегиями (**Запуск от имени администратора**) и выполните приведенные ниже команды. В этом сценарии мы предполагаем, что подключенному диску ОС присвоена буква F. Замените ее соответствующим значением для своей виртуальной машины.
2.  Проверьте, который протокол TLS включен.

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Если ключ не существует, или его значение **0**, включите протокол, выполнив следующие сценарии.

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Включите NLA.

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Отключите диск ОС и повторно создайте виртуальную машину](../windows/troubleshoot-recovery-disks-portal.md), а затем проверьте, устранена ли проблема.





