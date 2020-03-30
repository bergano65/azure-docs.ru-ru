---
title: Подготовка виртуального жесткого диска Windows к передаче в Azure
description: Узнайте, как подготовить Windows VHD или VHDX для загрузки в Azure
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
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250196"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Подготовка диска VHD или VHDX для Windows к отправке в Azure

Перед загрузкой виртуального компьютера Windows (VM) из помещения в Azure необходимо подготовить виртуальный жесткий диск (VHD или VHDX). Azure поддерживает vMs-инорезы поколения 1 и поколения 2, которые находятся в формате файлов VHD и имеют диск фиксированного размера. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ. 

В поколении 1 VM можно преобразовать файловую систему VHDX в VHD. Можно также преобразовать динамически расширяющийся диск на диск фиксированного размера. Но вы не можете изменить поколение виртуальной машины. Для получения дополнительной информации [см.](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) [Azure support for generation 2 VMs (preview)](generation-2.md)

Для получения информации о политике поддержки [Microsoft server software support for Azure VMs](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)для Пазур VMs см.

> [!NOTE]
> Инструкции в этой статье применяются к:
>1. 64-битная версия Windows Server 2008 R2 и более поздние операционные системы Windows Server. Информацию о запуске 32-разрядной операционной системы в Azure можно узнать в поддержке [32-разрядных операционных систем в Azure VMs.](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
>2. Если какой-либо инструмент аварийного восстановления будет использоваться для переноса рабочей нагрузки, например восстановление сайта Azure или Azure Migrate, этот процесс все еще требуется сделать и следовать на ОС для гостей, чтобы подготовить изображение до миграции.

## <a name="system-file-checker-sfc-command"></a>Команда System File Checker (SFC)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Выполнить Windows System File Checker утилита (запуск sfc /scannow) на ОС до обобщения шаг создания изображения ОС клиента

Команда System File Checker (SFC) используется для проверки и замены файлов системы Windows.

Для выполнения команды SFC:

1. Откройте повышенный CMD-запрос в качестве администратора.
1. Введите `sfc /scannow` и выберите **Enter**.

    ![ средство проверки системных файлов](media/prepare-for-upload-vhd-image/system-file-checker.png)


После завершения сканирования SFC попробуйте установить обновления Windows и перезапустить компьютер.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Преобразование виртуального диска в фиксированный размер и vHD

Если вам нужно преобразовать виртуальный диск в необходимый для Azure формат, используйте один из методов в этом разделе:

1. Резервное копирование VM перед запуском процесса преобразования виртуального диска.

1. Убедитесь, что Windows VHD работает правильно на локальном сервере. Устраните все ошибки на виртуальной машине, прежде чем преобразовывать ее или отправлять в Azure.

1. Что касается размера VHD:

   1. Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. Фракции мегабайта вызовут ошибки при создании изображений с загруженного VHD.

   2. Максимальный размер, разрешенный для OS VHD, составляет 2 ТБ.


После преобразования диска создайте VM, который использует диск. Начните и вопийте в VM, чтобы закончить подготовку его к загрузке.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Используйте Hyper-V Manager для преобразования диска 
1. Откройте диспетчер Hyper-V и выберите свой локальный компьютер в левой части окна. В меню выше списка компьютера, выберите **action** > **Edit Disk**.
2. На странице **Найти виртуальный жесткий диск,** выберите виртуальный диск.
3. На странице **«Выберите действие»** выберите **Convert** > **Next**.
4. Если вам нужно преобразовать из VHDX, выберите **VHD** > **Next.**
5. Если вам нужно преобразовать из динамически расширяющийся диск, выберите **Фиксированный размер** > **Next.**
6. Укажите путь к папке, в которой нужно сохранить новый VHD-файл.
7. Нажмите кнопку **Готово**.

> [!NOTE]
> Используйте повышенный сеанс PowerShell для выполнения команд в этой статье.

### <a name="use-powershell-to-convert-the-disk"></a>Используйте PowerShell для преобразования диска 
Виртуальный диск можно преобразовать с помощью команды [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) в Windows PowerShell. При запуске PowerShell выберите **Запуск от имени администратора**. 

Следующий пример команды преобразует диск с VHDX в VHD. Команда также преобразует диск с динамически расширяющегося диска на диск фиксированного размера.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

В этой команде замените `-Path` значение для с пути к виртуальному жесткому диску, который вы хотите преобразовать. Замените значение `-DestinationPath` для с новым путем и имя преобразованного диска.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Преобразование диска VMware в формате VMDK
Если у вас есть изображение Windows VM в [формате файла VMDK,](https://en.wikipedia.org/wiki/VMDK)используйте [конвертер виртуальной машины Microsoft](https://www.microsoft.com/download/details.aspx?id=42497) для преобразования его в формат VHD. Для получения дополнительной информации [см.](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)

## <a name="set-windows-configurations-for-azure"></a>Настройка конфигурации Windows для Azure

> [!NOTE]
> Платформа Azure устанавливает файл ISO на DVD-ROM, когда Windows VM создается из обобщенного изображения.
> По этой причине DVD-ROM должен быть включен в ОС в обобщеном изображении. Если он отключен, Windows VM будет застрял на OOBE.

На VM, который вы планируете загрузить в Azure, выполнить следующие команды из [окна повышенной команды:](https://technet.microsoft.com/library/cc947813.aspx)

1. Удалите все постоянные статические маршруты из таблицы маршрутизации.
   
   * Чтобы просмотреть таблицу маршрутов, запустите `route print` из окна командной строки.
   * Проверьте `Persistence Routes` разделы. Если есть постоянный маршрут, `route delete` используйте команду, чтобы удалить его.
2. Удалите прокси-сервер WinHTTP.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Если VM необходимо работать с определенным прокси, добавьте прокси-исключение в IP-адрес Azure ([168.63.129.16),](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)чтобы VM мог подключиться к Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Установите политику диска SAN на: [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)
   
    ```PowerShell
    diskpart 
    ```
    В открытом окне запроса команды введите следующие команды:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Установите координированное универсальное время (UTC) время для Windows. Также установите тип запуска службы`w32time`времени `Automatic`Windows () для:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Установите профиль питания на высокую производительность:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Убедитесь, что `TEMP` экологические переменные и `TMP` установлены на их значения по умолчанию:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Настройка служб Windows
Убедитесь, что для каждой из приведенных ниже служб Windows заданы значения Windows по умолчанию. Эти службы являются минимальными, которые должны быть созданы для обеспечения подключения VM. Чтобы сбросить параметры загрузки, можно использовать приведенные ниже команды.
   
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
## <a name="update-remote-desktop-registry-settings"></a>Обновление настроек реестра удаленного рабочего стола
Убедитесь, что следующие настройки настроены правильно для удаленного доступа:

>[!NOTE] 
>Сообщение об ошибке может `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`быть получено при запуске. Это сообщение можно игнорировать. Это означает только то, что домен не проталкивает эту конфигурацию через объект групповой политики.

1. Включите протокол удаленного рабочего стола (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Порт RDP настроен правильно. Порт по умолчанию 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    При развертывании виртуальной машины для порта 3389 создаются правила по умолчанию. Если требуется изменить номер порта, сделайте это после развертывания VM в Azure.

3. Прослушиватель должен работать в каждом сетевом интерфейсе:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Нанастройка режима аутентификации на уровне сети (NLA) для соединений RDP:
   
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
8. Удалите сертификаты, подписанные самостоятельно, привязанные к слушателю RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Этот код гарантирует, что вы можете подключиться в начале при развертывании VM. Если вам нужно просмотреть это позже, это можно сделать после развертывания VM в Azure.

9. Если VM будет частью домена, проверьте следующие политики, чтобы убедиться, что прежние настройки не будут восстановлены. 
    
    | Цель                                     | Политика                                                                                                                                                       | Значение                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Включение RDP                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения         | Разрешить пользователям удаленное подключение с использованием служб удаленных рабочих столов                                  |
    | Групповая политика с проверкой подлинности на уровне сети                         | Параметры\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Безопасность                                                    | Требуется проверка подлинности пользователя для удаленного доступа с помощью NLA |
    | Настройки keep-alive                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Настроить интервал проверяемых на активность подключений                                                 |
    | Параметры переподключения                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Повторное подключение автоматически                                                                   |
    | Ограниченное количество настроек соединения | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Ограничить количество подключений                                                              |

## <a name="configure-windows-firewall-rules"></a>Настройка правил брандмауэра Windows
1. Включите Брандмауэр Windows на трех профилях (домен, стандартный и общедоступный):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Запустите следующую команду в PowerShell, чтобы позволить WinRM через три профиля брандмауэра (домен, частный и общедоступный), и включить пульт дистанционного управления PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Включите следующие правила брандмауэра, чтобы разрешить трафик RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Включите правило для совместного использования файлов и принтеров, чтобы VM мог реагировать на команду пинг внутри виртуальной сети:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Создайте правило для платформы Azure:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Если VM будет частью домена, проверьте следующие политики Azure AD, чтобы убедиться, что прежние настройки не будут восстановлены. 

    | Цель                                 | Политика                                                                                                                                                  | Значение                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Включите профили брандмауэра Windows: | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Защита всех сетевых подключений         |
    | Включите RDP:                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    | Включите ICMP-V4:                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения ICMP                   |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения ICMP                   |

## <a name="verify-the-vm"></a>Проверка виртуальной машины 

Убедитесь, что VM является здоровым, безопасным и RDP доступным: 

1. Чтобы убедиться, что диск является здоровым и последовательным, проверьте диск при следующем перезапуске VM:

    ```PowerShell
    Chkdsk /f
    ```
    Убедитесь, что в отчете отображается чистый и здоровый диск.

2. Задайте параметры данных конфигурации загрузки. 

    > [!NOTE]
    > Используйте повышенное окно PowerShell для выполнения этих команд.
   
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
3. Лог свалки может быть полезен при устранении проблем с сбоя Windows. Включить сбор дампа:

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
4. Убедитесь, что репозиторий управления Windows (WMI) является последовательным:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Если репозиторий поврежден, [см. WMI: Репозиторий коррупции или нет](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Убедитесь, что ни одно другое приложение не использует порт 3389. Этот порт используется для службы RDP в Azure. Чтобы узнать, какие порты используются на VM, запустите: `netstat -anob`

    ```PowerShell
    netstat -anob
    ```

6. Чтобы загрузить Windows VHD, который является контроллером домена:

   * Выполните [эти дополнительные шаги](https://support.microsoft.com/kb/2904015), чтобы подготовить диск.

   * Убедитесь, что вы знаете режим восстановления служб каталогов (DSRM) пароль в случае, если вам придется запустить VM в DSRM в какой-то момент. Для получения дополнительной информации [см.](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)

7. Убедитесь, что вы знаете встроенную учетную запись администратора и пароль. Возможно, вы захотите сбросить текущий пароль локального администратора и убедиться, что вы можете использовать эту учетную запись для вхушения в Windows через соединение RDP. Это разрешение доступа контролируется "Разрешить войти в систему через удаленных настольных служб" Групповой объект политики. Посмотреть этот объект в редакторе политики локальной группы можно здесь:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Проверьте следующие политики Azure AD, чтобы убедиться, что вы не блокируете доступ RDP через RDP или из сети:

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Отказ в доступе к компьютеру из сети;

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Запретить вход в систему через службу удаленных рабочих столов.


9. Проверьте следующую политику Azure AD, чтобы убедиться, что вы не удаляете ни одной из необходимых учетных записей доступа:

   - Настройка компьютерной конфигурации и настройки Windows,Настройки безопасности,местные политики»Назначение прав пользователя/Доступ к этому компьютеру из сети

   Политика должна перечислять следующие группы:

   - Администраторы

   - Операторы архивации

   - Все

   - Пользователи

10. Перезапустите VM, чтобы убедиться, что Windows по-прежнему здорова и может быть достигнута через rdP соединение. На этом этапе вы можете создать VM в локальном Hyper-V, чтобы убедиться, что VM запускается полностью. Затем проверьте, чтобы убедиться, что вы можете достичь VM через RDP.

11. Удалите все дополнительные фильтры интерфейса драйвера транспорта (TDI). Например, удалите программное обеспечение, которое анализирует пакеты TCP или дополнительные брандмауэры. Если вам нужно просмотреть это позже, это можно сделать после развертывания VM в Azure.

12. Установить любое другое стороннее программное обеспечение или драйвер, связанный с физическими компонентами или любой другой технологией виртуализации.

### <a name="install-windows-updates"></a>Установка обновлений Windows
В идеале, вы должны держать машину обновляется на *уровне патча.* Если это невозможно, убедитесь, что установлены следующие обновления. Чтобы получить последние обновления, смотрите страницы истории обновления Windows: [Windows 10 и Windows Server 2019,](https://support.microsoft.com/help/4000825) [Windows 8.1 и Windows Server 2012 R2](https://support.microsoft.com/help/4009470) и [Windows 7 SP1 и Windows Server 2008 R2 SP1.](https://support.microsoft.com/help/4009469)

| Компонент               | Двоичные данные         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 версии 1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
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
| Основные сведения                    | ntoskrnl.exe   | 6.1.7601.23807 — KB4022719                | 6.2.9200.22170 — KB4022718                  | 6.3.9600.18696 — KB4022726         | 10.0.14393.1358 — KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
> Чтобы избежать случайной перезагрузки во время подготовки VM, мы рекомендуем обеспечить, чтобы все установки обновления Windows были закончены и чтобы обновления не были ожидаемы. Один из способов сделать это , чтобы установить все возможные обновления Windows и перезагрузки один раз, прежде чем запустить команду Sysprep.

### <a name="determine-when-to-use-sysprep"></a>Определить, когда использовать Sysprep<a id="step23"></a>    

Инструмент подготовки системы (Sysprep) — это процесс, который можно запустить для сбросить установку Windows. Sysprep обеспечивает "из коробки" опыт, удалив все личные данные и сброс нескольких компонентов. 

Обычно вы используете Sysprep для создания шаблона, из которого можно развернуть несколько других вс-ч с определенной конфигурацией. Шаблон называется *обобщенный образ.*

Если вы хотите создать только один VM с одного диска, вам не придется использовать Sysprep. Вместо этого можно создать VM из *специализированного изображения.* Для получения информации о том, как создать VM со специализированного диска, см.:

- [Создание виртуальной машины Windows из специализированного диска](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master) (Создание виртуальной машины из специализированного VHD-диска)

Если вы хотите создать обобщенный образ, вам нужно запустить Sysprep. Для получения дополнительной информации, [см. Как использовать Sysprep: Введение](https://technet.microsoft.com/library/bb457073.aspx). 

Не каждая роль или приложение, установленное на компьютере на базе Windows, поддерживает обобщенные изображения. Поэтому, прежде чем запустить эту процедуру, убедитесь, что Sysprep поддерживает роль компьютера. Дополнительные сведения см. в статье о [поддержке ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Обобщание VHD

>[!NOTE]
> После запуска `sysprep.exe` в следующих шагах выключите VM. Не включайте его обратно, пока не создадите изображение из него в Azure.

1. Войдите на виртуальную машину Windows.
1. Запустите **командную строку** от имени администратора. 
1. Измените каталог `%windir%\system32\sysprep`на . Затем выполните `sysprep.exe`.
1. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.

    ![Средство SysPrep](media/prepare-for-upload-vhd-image/syspre.png)
1. В окне **Параметры завершения работы** выберите параметр **Завершение работы**.
1. Нажмите кнопку **ОК**.
1. Когда Сиспреп закончит, выключите VM. Не используйте **Restart,** чтобы выключить VM.

Теперь диск VHD можно отправлять. Для получения дополнительной информации о том, как создать VM с обобщенного диска, [см. Загрузите обобщенный VHD и используйте его для создания нового VM в Azure.](sa-upload-generalized.md)


>[!NOTE]
> Пользовательский файл *unattend.xml* не поддерживается. Хотя мы поддерживаем `additionalUnattendContent` свойство, это обеспечивает лишь ограниченную поддержку для добавления [опций Microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) в файл *unattend.xml,* который использует агент подготовки Azure. Вы можете использовать, например, [дополнительныйUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) для добавления FirstLogonCommands и LogonCommands. Для получения дополнительной информации смотрите [дополнительные примеры UnattendContent FirstLogonCommands.](https://github.com/Azure/azure-quickstart-templates/issues/1407)


## <a name="complete-the-recommended-configurations"></a>Заполните рекомендуемые конфигурации
Следующие настройки не влияют на загрузку VHD. Тем не менее, мы настоятельно рекомендуем их настроить.

* Установите [виртуальный машинный агент Azure.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) Затем можно включить расширения виртуальной машины. Расширения VM реализуют большую часть критических функций, которые вы можете использовать с помощью vMs. Вам понадобятся расширения, например, для сбросить пароли или настроить RDP. Для получения дополнительной информации смотрите [обзор агента виртуальной машины Azure](../extensions/agent-windows.md).
* После создания VM в Azure мы рекомендуем поместить файл страницы на *объем временного диска* для повышения производительности. Вы можете настроить размещение файла следующим образом:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Если диск данных прикреплен к VM, буква объема временного диска обычно *D*. Это обозначение может быть различным, в зависимости от настроек и количества доступных дисков.
  * Мы рекомендуем отключить блокировщики скриптов, которые могут быть предоставлены антивирусным программным обеспечением. Они могут вмешиваться и блокировать скрипты агента подготовки Windows, выполняемые при развертывании нового VM с вашего изображения.
  
## <a name="next-steps"></a>Дальнейшие действия
* [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](upload-generalized-managed.md)
* [Проблемы с устранением проблем Azure Windows VM](troubleshoot-activation-problems.md)

