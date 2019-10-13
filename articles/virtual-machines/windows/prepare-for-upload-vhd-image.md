---
title: Подготовка виртуального жесткого диска Windows к передаче в Azure | Документация Майкрософт
description: Узнайте, как подготовить VHD или VHDX Windows к отправке в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 555b250f211cf22e766e64960b3359692f73c843
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285716"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Подготовка диска VHD или VHDX для Windows к отправке в Azure

Перед отправкой виртуальной машины Windows из локальной среды в Azure необходимо подготовить виртуальный жесткий диск (VHD или VHDX). Azure поддерживает виртуальные машины поколения 1 и 2, которые имеют формат VHD-файла и имеют диск фиксированного размера. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ. 

В виртуальной машине поколения 1 можно преобразовать VHDX-файловую систему в VHD. Кроме того, можно преобразовать динамически расширяемый диск в диск фиксированного размера. Но вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. [в статье Создание виртуальной машины поколения 1 или 2 в Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) и [Поддержка Azure для виртуальных машин поколения 2 (Предварительная версия)](generation-2.md).

Сведения о политике поддержки для виртуальных машин Azure см. в [статье поддержка серверного программного обеспечения Майкрософт для виртуальных машин Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Инструкции, приведенные в этой статье, относятся к:
>1. 64-разрядная версия операционных систем Windows Server 2008 R2 и более поздних версий. Сведения о выполнении 32-разрядной операционной системы в Azure см. [в статье поддержка 32-разрядных операционных систем на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Если для миграции рабочей нагрузки будет использоваться какое-либо средство аварийного восстановления, например Azure Site Recovery или Azure Migration, этот процесс все еще необходимо выполнить и затем приступать к гостевой ОС, чтобы подготовить образ перед миграцией.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Преобразование виртуального диска в фиксированный размер и на виртуальный жесткий диск

Если необходимо преобразовать виртуальный диск в требуемый формат для Azure, используйте один из методов, описанных в этом разделе.

1. Создайте резервную копию виртуальной машины перед выполнением преобразования виртуального диска.

1. Убедитесь, что виртуальный жесткий диск Windows правильно работает на локальном сервере. Устраните все ошибки на виртуальной машине, прежде чем преобразовывать ее или отправлять в Azure.

1. О размере виртуального жесткого диска:

   1. Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. Доли мегабайта могут вызвать ошибки при создании изображений из отправленного виртуального жесткого диска.

   2. Максимально допустимый размер VHD для операционной системы — 2 ТБ.


После преобразования диска создайте виртуальную машину, использующую этот диск. Запустите и войдите в виртуальную машину, чтобы завершить ее подготовку к отправке.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Преобразование диска с помощью диспетчера Hyper-V 
1. Откройте диспетчер Hyper-V и выберите свой локальный компьютер в левой части окна. В меню над списком компьютер выберите **действие** > **изменить диск**.
2. На странице **Обнаружение виртуального жесткого диска** выберите виртуальный диск.
3. На странице **Выбор действия** выберите **преобразовать** > **Далее**.
4. Если необходимо выполнить преобразование из VHDX, выберите **VHD** > **Далее**.
5. Если необходимо преобразовать динамически расширяемый диск, выберите **фиксированный размер** > **Далее**.
6. Укажите путь к папке, в которой нужно сохранить новый VHD-файл.
7. Выберите **Готово**.

> [!NOTE]
> Используйте сеанс PowerShell с повышенными привилегиями для выполнения команд, описанных в этой статье.

### <a name="use-powershell-to-convert-the-disk"></a>Преобразование диска с помощью PowerShell 
Виртуальный диск можно преобразовать с помощью команды [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) в Windows PowerShell. При запуске PowerShell выберите **Запуск от имени администратора**. 

В следующем примере команда преобразует диск из формата VHDX в VHD. Команда также преобразует диск из динамически расширяющегося диска в диск фиксированного размера.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

В этой команде замените значение `-Path` на путь к виртуальному жесткому диску, который требуется преобразовать. Замените значение `-DestinationPath` новым путем и именем преобразованного диска.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Преобразование диска VMware в формате VMDK
Если вы используете образ виртуальной машины Windows в [формате VMDK](https://en.wikipedia.org/wiki/VMDK), используйте [Преобразователь виртуальной машины Microsoft](https://www.microsoft.com/download/details.aspx?id=42497) для преобразования его в формат VHD. Дополнительные сведения см. в статье [Преобразование виртуального жесткого диска VMware в виртуальный жесткий диск Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Настройка конфигурации Windows для Azure

На виртуальной машине, которую планируется отправить в Azure, выполните следующие команды в [окне командной строки с повышенными привилегиями](https://technet.microsoft.com/library/cc947813.aspx):

1. Удалите все постоянные статические маршруты из таблицы маршрутизации.
   
   * Чтобы просмотреть таблицу маршрутов, запустите `route print` из окна командной строки.
   * Проверьте `Persistence Routes` разделов. Если имеется постоянный маршрут, удалите его с помощью команды `route delete`.
2. Удалите прокси-сервер WinHTTP.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Если виртуальная машина должна работать с конкретным прокси-сервером, добавьте исключение прокси-сервера в адрес IP Azure ([168.63.129.16 @ no__t-1), чтобы виртуальная машина могла подключаться к Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Задайте для политики SAN диска значение [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    В открытом окне командной строки введите следующие команды:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Установить время в формате UTC для Windows. Также задайте для параметра Тип запуска службы времени Windows (`w32time`) значение `Automatic`.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Установите для профиля питания высокую производительность:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Убедитесь, что для переменных среды `TEMP` и `TMP` заданы значения по умолчанию:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Настройка служб Windows
Убедитесь, что для каждой из следующих служб Windows заданы значения по умолчанию для Windows. Эти службы являются минимальными, которые необходимо настроить для обеспечения подключения к виртуальной машине. Чтобы сбросить параметры загрузки, можно использовать приведенные ниже команды.
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```

## <a name="update-remote-desktop-registry-settings"></a>Обновление параметров реестра удаленного рабочего стола
Убедитесь, что для удаленного доступа правильно настроены следующие параметры:

>[!NOTE] 
>При запуске `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>` может появиться сообщение об ошибке. Это сообщение можно спокойно проигнорировать. Это означает только то, что домен не помещает эту конфигурацию через объект групповая политика.

1. Включите протокол удаленного рабочего стола (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Порт RDP настроен правильно. Порт по умолчанию — 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    При развертывании виртуальной машины для порта 3389 создаются правила по умолчанию. Если вы хотите изменить номер порта, сделайте это после развертывания виртуальной машины в Azure.

3. Прослушиватель должен работать в каждом сетевом интерфейсе:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Настройте режим проверки подлинности на уровне сети (NLA) для подключений по протоколу RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Задайте значение для проверки активности:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Выполните переподключение:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Ограничьте количество одновременных подключений:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Удалите все самозаверяющие сертификаты, привязанные к прослушивателю RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Этот код гарантирует, что вы сможете подключиться в начале при развертывании виртуальной машины. Если вы хотите проверить это позднее, это можно сделать после развертывания виртуальной машины в Azure.

9. Если виртуальная машина будет входить в домен, проверьте следующие политики, чтобы убедиться, что прежние параметры не были отменены. 
    
    | Цель                                     | Политика                                                                                                                                                       | Значение                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Включение RDP                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения         | Разрешить пользователям удаленное подключение с использованием служб удаленных рабочих столов                                  |
    | Групповая политика с проверкой подлинности на уровне сети                         | Параметры\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Безопасность                                                    | Требовать проверку подлинности пользователя для удаленного доступа с помощью NLA |
    | Параметры сохранения активности                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Настроить интервал проверяемых на активность подключений                                                 |
    | Параметры переподключения                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Автоматическое подключение                                                                   |
    | Ограниченное число параметров подключения | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Ограничить количество подключений                                                              |

## <a name="configure-windows-firewall-rules"></a>Настройка правил брандмауэра Windows
1. Включите брандмауэр Windows в трех профилях (домен, Стандартный и общедоступный):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Выполните следующую команду в PowerShell, чтобы разрешить WinRM через три профиля брандмауэра (домен, частный и общедоступный), а затем включите удаленную службу PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Включите следующие правила брандмауэра, чтобы разрешить трафик RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Включите правило для общего доступа к файлам и принтерам, чтобы виртуальная машина могла отвечать на команду ping в виртуальной сети:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Если виртуальная машина будет входить в домен, проверьте следующие политики Azure AD, чтобы убедиться, что прежние параметры не были отменены. 

    | Цель                                 | Политика                                                                                                                                                  | Значение                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Включите профили брандмауэра Windows: | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Защита всех сетевых подключений         |
    | Включите RDP:                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    | Включите ICMP-V4:                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения ICMP                   |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения ICMP                   |

## <a name="verify-the-vm"></a>Проверка виртуальной машины 

Убедитесь, что виртуальная машина работоспособна, защищена и RDP доступна: 

1. Чтобы убедиться, что диск является работоспособным и последовательным, проверьте диск при следующей перезагрузке виртуальной машины:

    ```PowerShell
    Chkdsk /f
    ```
    Убедитесь, что в отчете отображается чистый и работоспособный диск.

2. Задайте параметры данных конфигурации загрузки. 

    > [!NOTE]
    > Используйте окно PowerShell с повышенными привилегиями для выполнения этих команд.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Журнал дампа может быть полезен при устранении неполадок, связанных с аварийным завершением работы Windows. Включите сбор журналов дампа:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Убедитесь, что репозиторий инструментарий управления Windows (WMI) (WMI) является согласованным:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Если репозиторий поврежден, ознакомьтесь с [этим разделом Повреждение репозитория или не @ no__t-0.

5. Убедитесь, что никакое другое приложение не использует порт 3389. Этот порт используется для службы RDP в Azure. Чтобы узнать, какие порты используются на виртуальной машине, выполните `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Чтобы отправить виртуальный жесткий диск Windows, который является контроллером домена, выполните следующие действия.

   * Выполните [эти дополнительные шаги](https://support.microsoft.com/kb/2904015), чтобы подготовить диск.

   * Убедитесь, что вы знаете пароль режима восстановления служб каталогов (DSRM) на случай, если в какой-то момент потребуется запустить виртуальную машину в режиме DSRM. Дополнительные сведения см. [в разделе Установка пароля DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Убедитесь, что вы знаете встроенную учетную запись администратора и пароль. Вам может потребоваться сбросить текущий пароль локального администратора и убедиться, что вы можете использовать эту учетную запись для входа в Windows через подключение по протоколу RDP. Это разрешение на доступ управляется объектом групповая политика "разрешить вход через службы удаленных рабочих столов". Просмотрите этот объект в редактор локальных групповых политик здесь:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Проверьте следующие политики Azure AD и убедитесь, что вы не блокируете доступ к RDP через RDP или из сети:

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Отказ в доступе к компьютеру из сети;

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Запретить вход в систему через службу удаленных рабочих столов.


9. Проверьте следующую политику Azure AD и убедитесь, что вы не удаляете ни одну из необходимых учетных записей доступа:

   - Конфигурация компьютера \ параметры безопасности \ локальные сетевые Policies\User права Ассигнмент\акцесс этот компьютер из сети

   Политика должна перечислять следующие группы:

   - Администраторы

   - Операторы архивации

   - Все

   - Пользователи

10. Перезапустите виртуальную машину, чтобы убедиться в работоспособности Windows и доступе через RDP-подключение. На этом этапе может потребоваться создать виртуальную машину в локальном Hyper-V, чтобы убедиться, что виртуальная машина запускается полностью. Затем протестируйте, чтобы убедиться, что вы можете подключиться к виртуальной машине по протоколу RDP.

11. Удалите все лишние фильтры TDI (TDI). Например, удалите программное обеспечение, которое анализирует TCP-пакеты или дополнительные брандмауэры. Если вы хотите проверить это позднее, это можно сделать после развертывания виртуальной машины в Azure.

12. Удалите все другие сторонние программы или драйверы, связанные с физическими компонентами или любыми другими технологиями виртуализации.

### <a name="install-windows-updates"></a>Установка обновлений Windows
В идеале следует обновлять компьютер на *уровне обновления*. Если это невозможно, убедитесь, что установлены следующие обновления:

| Компонент               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Хранилище                 | disk.sys       | 6.1.7601.23403 — KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 — KB3137061 | 6.3.9600.18203 — KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 — KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 — KB3018489 | 6.3.9600.18573 — KB4022726         | 10.0.14393.1358 — KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 — KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 — KB3121255 | 6.3.9600.18654 — KB4022726         | 10.0.14393.1198 — KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 — KB3125574                | 6.2.9200.16384 — KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 — KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 — KB2995387 | 6.3.9600.18334 — KB3172614         | 10.0.14393.953 — KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 — KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 — KB2975331 | 6.3.9600.18265 — KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 — KB3125574                | 6.2.9200.16681 — KB2877114                  | 6.3.9600.17401 — KB3000850         | 10.0.14393.953 — KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 — KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 — KB3125574                | 6.2.9200.21006 — KB2955163                  | 6.3.9600.18624 — KB4022726         | 10.0.14393.1066 — KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 — KB3125574                | 6.2.9200.21474 — KB3046101                  | 6.3.9600.18592 — KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 — KB3125574                | 6.2.9200.21190 — KB3046101                  | 6.3.9600.18616 — KB4022726         | 10.0.14393.1198 — KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 — KB4072650                | 6.3.9600.18080 — KB3063109                  | 6.3.9600.18907 — KB4072650         | 10.0.14393.2007 — KB4345418                             | 10.0.15063.850 — KB4345419 | 10.0.16299.371 — KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 — KB3125574                | 6.2.9200.20930 — KB2930244                  | 6.3.9600.18294 — KB3172614         | 10.0.14393.576 — KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 — KB3125574                | 6.2.9200.20930 — KB2930244                  | 6.3.9600.17415 — KB3172614         | 10.0.14393.206 — KB4022715                              | -                          | -                                               | -                                               |
| Сеть                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 — KB4022715                             | 10.0.15063.250 — KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 — KB4022722                | 6.2.9200.22108 — KB4022724                  | 6.3.9600.18603 — KB4022726         | 10.0.14393.479 — KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 — KB4022722                | 6.2.9200.21548 — KB4022724                  | 6.3.9600.18586 — KB4022726         | 10.0.14393.953 — KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 — KB4022722                | 6.2.9200.22074 — KB4022724                  | 6.3.9600.18586 — KB4022726         | 10.0.14393.953 — KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 — KB4022722                | 6.2.9200.22070 — KB4022724                  | 6.3.9600.18478 — KB4022726         | 10.0.14393.1358 — KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 — KB3125574                | 6.2.9200.17285 — KB3042553                  | 6.3.9600.18574 — KB4022726         | 10.0.14393.251 — KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 — KB4022719                | 6.2.9200.22117 — KB4022724                  | 6.3.9600.18654 — KB4022726         | 10.0.14393.1358 — KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 — KB4022719                | 6.2.9200.22170 — KB4022718                  | 6.3.9600.18696 — KB4022726         | 10.0.14393.1358 — KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Службы удаленных рабочих столов | rdpcorets.dll  | 6.2.9200.21506 — KB4022719                | 6.2.9200.22104 — KB4022724                  | 6.3.9600.18619 — KB4022726         | 10.0.14393.1198 — KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 — KB3125574                | 6.2.9200.17048 — KB2973501                  | 6.3.9600.17415 — KB3000850         | 10.0.14393.0 — KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 — KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 — KB4022719                | 6.2.9200.22168 — KB4022718                  | 6.3.9600.18698 — KB4022726         | 10.0.14393.594 — KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 — KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 — KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Безопасность                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Чтобы избежать случайной перезагрузки во время подготовки виртуальной машины, рекомендуется убедиться, что все Центр обновления Windows установки завершены и обновления не ожидаются. Один из способов сделать это — установить все возможные обновления Windows и перезагрузить один раз перед выполнением команды Sysprep.

### Определение необходимости использования Sysprep<a id="step23"></a>    

Средство подготовки системы (Sysprep) — это процесс, который можно выполнить для сброса установки Windows. Средство Sysprep обеспечивает работу, удаляя все персональные данные и переустанавливая несколько компонентов. 

Как правило, средство Sysprep запускается для создания шаблона, из которого можно развернуть несколько других виртуальных машин с определенной конфигурацией. Шаблон называется *обобщенным изображением*.

Если вы хотите создать только одну виртуальную машину с одного диска, не нужно использовать Sysprep. Вместо этого можно создать виртуальную машину на основе *специализированного образа*. Сведения о том, как создать виртуальную машину на основе специализированного диска, см. в следующих статьях:

- [Создание виртуальной машины Windows из специализированного диска](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master) (Создание виртуальной машины из специализированного VHD-диска)

Если вы хотите создать обобщенный образ, необходимо запустить Sysprep. Дополнительные сведения см. в разделе [How to use Sysprep: Введение @ no__t-0. 

Не все роли или приложения, установленные на компьютере под управлением Windows, поддерживают обобщенные образы. Поэтому перед выполнением этой процедуры убедитесь, что Sysprep поддерживает роль компьютера. Дополнительные сведения см. в статье о [поддержке ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Подготовка виртуального жесткого диска к использованию

>[!NOTE]
> После запуска `sysprep.exe` на следующих шагах выключите виртуальную машину. Не включайте его снова, пока вы не создадите образ в Azure.

1. Войдите на виртуальную машину Windows.
1. Запустите **командную строку** от имени администратора. 
1. Измените каталог на `%windir%\system32\sysprep`. Затем выполните `sysprep.exe`.
1. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.

    ![Средство SysPrep](media/prepare-for-upload-vhd-image/syspre.png)
1. В разделе **Параметры завершения работы** выберите **Завершение работы**.
1. Нажмите кнопку **ОК**.
1. После завершения работы программы Sysprep завершите работу виртуальной машины. Не используйте **перезагрузку** для завершения работы виртуальной машины.

Теперь диск VHD можно отправлять. Дополнительные сведения о том, как создать виртуальную машину на основе обобщенного диска, см. в разделе [Отправка обобщенного виртуального жесткого диска и его использование для создания новой виртуальной машины в Azure](sa-upload-generalized.md).


>[!NOTE]
> Пользовательский файл *Unattend. XML* не поддерживается. Хотя мы поддерживаем свойство `additionalUnattendContent`, которое предоставляет ограниченную поддержку добавления параметров [Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) в файл *Unattend. XML* , используемый агентом подготовки Azure. Можно использовать, например, [аддитионалунаттендконтент](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) , чтобы добавить Фирстлогонкоммандс и логонкоммандс. Дополнительные сведения см. в разделе [Аддитионалунаттендконтент фирстлогонкоммандс example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Выполнение рекомендуемых конфигураций
Следующие параметры не влияют на отправку виртуального жесткого диска. Тем не менее, мы настоятельно рекомендуем их настроить.

* Установите [Агент виртуальной машины Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Затем можно включить расширения виртуальной машины. Расширения виртуальных машин реализуют большинство важных функций, которые могут потребоваться для использования с виртуальными машинами. Для сброса паролей или настройки протокола удаленного рабочего стола требуются расширения, например. Дополнительные сведения см. в [обзоре агента виртуальной машины Azure](../extensions/agent-windows.md).
* После создания виртуальной машины в Azure рекомендуется разместить файл подкачки на *томе временного диска* , чтобы повысить производительность. Расположение файла можно настроить следующим образом:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Если диск данных подключен к виртуальной машине, то буква тома временного диска обычно равна *D*. Это может быть различным, в зависимости от параметров и количества доступных дисков.
  * Рекомендуется отключить блокирование сценариев, которое может быть предоставлено антивирусным программным обеспечением. Они могут помешать и блокировать сценарии агента подготовки Windows, выполняемые при развертывании новой виртуальной машины из образа.
  
## <a name="next-steps"></a>Следующие шаги
* [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](upload-generalized-managed.md)
* [Устранение неполадок при активации виртуальной машины Windows в Azure](troubleshoot-activation-problems.md)

