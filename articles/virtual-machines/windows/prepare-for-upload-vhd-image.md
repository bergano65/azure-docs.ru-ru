---
title: Подготовка виртуального жесткого диска Windows к передаче в Azure | Документация Майкрософт
description: Узнайте, как подготовка Windows VHD или VHDX для отправки его в Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: f40b3e0d2a49f6522149a977572d4f3c12e34255
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720064"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Подготовка диска VHD или VHDX для Windows к отправке в Azure

Перед отправкой Windows виртуальной машины (VM) на платформу Azure, необходимо подготовить виртуальный жесткий диск (VHD или VHDX). Azure поддерживает как первого, так и виртуальные машины поколения 2, в формат VHD и имеющие диск фиксированного размера. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ. 

В поколении 1 виртуальной Машины, можно преобразовать файловую систему VHDX в VHD. Можно также преобразовать динамически расширяемый диск на диск фиксированного размера. Но вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. в разделе [нужно создавать поколение 1 или 2 виртуальной Машины в Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) и [поддержки Azure для создания виртуальной машиной (Предварительная версия)](generation-2.md).

Сведения о политике поддержки для виртуальных машин Azure, см. в разделе [поддержка серверного программного обеспечения Майкрософт для виртуальных машин Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Инструкции в этой статье применимы к 64-разрядной версии Windows Server 2008 R2 и более поздних операционных системах Windows Server. Сведения о 32-разрядной операционной системой в Azure, см. в разделе [поддержка 32-разрядных операционных системах на виртуальных машинах Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Преобразовать виртуальный диск фиксированного размера и для виртуального жесткого диска 
Если вы хотите преобразовать виртуальный диск в формат, поддерживаемый Azure, используйте один из методов, указанных в этом разделе. Резервное копирование виртуальной Машины перед началом преобразования виртуального диска. Убедитесь, что виртуальный жесткий ДИСК Windows правильно работает на локальном сервере. Затем устраните все ошибки в самой виртуальной Машине, прежде чем преобразовывать ее или отправлять в Azure.

После преобразования диска создайте виртуальную Машину, использующую диск. Запуск и вход на виртуальную машину для завершения подготовки его для передачи.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Преобразовать диск с помощью диспетчера Hyper-V 
1. Откройте диспетчер Hyper-V и выберите свой локальный компьютер в левой части окна. В меню над списком компьютеров, выберите **действие** > **изменить диск**.
2. На **поиск виртуального жесткого диска** выберите виртуальный диск.
3. На **выбрать действие** выберите **преобразовать** > **Далее**.
4. Если необходимо преобразовать из формата VHDX, выберите **VHD** > **Далее**.
5. Если необходимо преобразовать динамически расширяемый диск, выберите **фиксированный размер** > **Далее**.
6. Укажите путь к папке, в которой нужно сохранить новый VHD-файл.
7. Выберите **Готово**.

> [!NOTE]
> Используйте сеанс PowerShell с повышенными правами для выполнения команд в этой статье.

### <a name="use-powershell-to-convert-the-disk"></a>Преобразовать диск с помощью PowerShell 
Виртуальный диск можно преобразовать с помощью команды [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) в Windows PowerShell. При запуске PowerShell выберите **Запуск от имени администратора**. 

Например, следующая команда преобразует диск из формата VHDX в VHD. Команда также преобразует диск из динамически расширяемый диск на диск фиксированного размера.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

В этой команде замените значение `-Path` с путь виртуального жесткого диска, который требуется преобразовать. Замените значение `-DestinationPath` с новым путем и именем преобразованного диска.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Преобразование диска VMware в формате VMDK
Если у вас есть образ виртуальной Машины Windows [формате VMDK](https://en.wikipedia.org/wiki/VMDK), использовать [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) для преобразования его в формат VHD. Дополнительные сведения см. в разделе [преобразование VMware VMDK в VHD Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Настройка конфигурации Windows для Azure

На виртуальной Машине, которую планируется отправить в Azure, выполните следующие команды из [окно командной строки с повышенными](https://technet.microsoft.com/library/cc947813.aspx):

1. Удалите все постоянные статические маршруты из таблицы маршрутизации.
   
   * Чтобы просмотреть таблицу маршрутов, запустите `route print` из окна командной строки.
   * Проверьте `Persistence Routes` разделы. При наличии постоянного маршрута, воспользуйтесь `route delete` команду, чтобы удалить его.
2. Удалите прокси-сервер WinHTTP.
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Если виртуальная машина должна работать с определенной прокси-сервер, добавьте исключение прокси-сервера в Azure IP-адрес ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), виртуальную Машину для подключения к Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Установите для политики SAN дисков [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    В окне откройте окно командной строки введите следующие команды:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Задать время в формате UTC (UTC) для Windows. Также задать тип запуска службы времени Windows (`w32time`) для `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Установите профиль управления питанием высокую производительность:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Убедитесь, что переменные среды `TEMP` и `TMP` задаются значения по умолчанию:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Настройка служб Windows
Убедитесь, что каждый из следующих служб Windows имеет значение Windows значения по умолчанию. Эти службы являются минимум, необходимо настроить, чтобы обеспечить подключение виртуальной Машины. Чтобы сбросить параметры загрузки, можно использовать приведенные ниже команды.
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Обновление параметров реестра удаленному рабочему столу
Убедитесь, что следующие параметры настроены правильно для удаленного доступа:

>[!NOTE] 
>Может появиться сообщение об ошибке при выполнении `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. Это сообщение можно игнорировать. Оно означает, что домен не отправляет эту конфигурацию через объект групповой политики.

1. Включите протокол удаленного рабочего стола (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Порт протокола удаленного рабочего СТОЛА настроено правильно. По умолчанию используется порт 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    При развертывании виртуальной машины для порта 3389 создаются правила по умолчанию. Если вы хотите изменить номер порта, сделайте это после развертывания виртуальной Машины в Azure.

3. Прослушиватель должен работать в каждом сетевом интерфейсе:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Настройте проверку подлинности на сетевом уровне (NLA) для подключений RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Задайте значение для проверки активности:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Выполните переподключение:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Ограничьте количество одновременных подключений:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Удалите все самозаверяющие сертификаты, к прослушивателю RDP привязаны:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Этот код гарантирует, что вы можете подключиться в начале при развертывании виртуальной Машины. Если вам нужно более поздней версии см. в этом, это можно сделать после развертывания виртуальной Машины в Azure.

9. Если виртуальная машина будет частью домена, проверьте следующие политики, чтобы убедиться в том, что не вернуть прежние параметры. 
    
    | Цель                                     | Политика                                                                                                                                                       | Значение                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Включение RDP                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения         | Разрешить пользователям удаленное подключение с использованием служб удаленных рабочих столов                                  |
    | Групповая политика с проверкой подлинности на уровне сети                         | Параметры\Административные шаблоны\Компоненты\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Безопасность                                                    | Требовать проверку подлинности для удаленного доступа с помощью NLA |
    | Параметры проверки активности                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Настроить интервал проверяемых на активность подключений                                                 |
    | Параметры переподключения                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Повторное соединение производится автоматически                                                                   |
    | Ограниченное число параметров подключения | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Компоненты Windows\Службы удаленных рабочих столов\Узел сеансов удаленных рабочих столов\Подключения | Ограничить количество подключений                                                              |

## <a name="configure-windows-firewall-rules"></a>Настройка правил брандмауэра Windows
1. Включение брандмауэра Windows для трех профилей (домен, standard и общедоступный):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Выполните следующую команду в PowerShell, чтобы разрешить трафик WinRM в трех профилях брандмауэра (домен, частных и общедоступных) и включите службу удаленного PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Включите следующие правила брандмауэра, чтобы разрешить трафик RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Включение правила для файлов и принтеров, общий доступ, чтобы виртуальная машина может отвечать на команду ping внутри виртуальной сети:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Если виртуальная машина будет частью домена, проверьте следующие политики Azure AD, чтобы убедиться в том, что не вернуть прежние параметры. 

    | Цель                                 | Политика                                                                                                                                                  | Значение                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Включите профили брандмауэра Windows: | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Защита всех сетевых подключений         |
    | Включите RDP:                           | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения для входящих сообщений удаленного управления рабочим столом |
    | Включите ICMP-V4:                       | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Профиль домена\Брандмауэр Windows   | Разрешить исключения ICMP                   |
    |                                      | Конфигурация компьютера\Политики\Параметры Windows\Административные шаблоны\Сеть\Сетевое подключение\Брандмауэр Windows\Стандартный профиль\Брандмауэр Windows | Разрешить исключения ICMP                   |

## <a name="verify-the-vm"></a>Проверка виртуальной машины 

Убедитесь, что виртуальная машина находится в работоспособном и безопасной и доступен по протоколу RDP: 

1. Чтобы убедиться, что диск работоспособен и согласован, проверьте диск при следующей перезагрузке виртуальной Машины:

    ```PowerShell
    Chkdsk /f
    ```
    Убедитесь, что в отчете отображаются исправный диск без.

2. Задайте параметры данных конфигурации загрузки. 

    > [!NOTE]
    > Используйте окно PowerShell с повышенными привилегиями для выполнения этих команд.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Журнал дампа может быть полезна для устранения неполадок Windows. Включите сбор журнала дампа:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Проверьте согласованность репозитория инструментария управления Windows (WMI):

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Если репозиторий поврежден, ознакомьтесь с [этим разделом Повреждение базы данных или не](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Убедитесь, что никакое другое приложение использует порт 3389. Этот порт используется для службы RDP в Azure. Чтобы узнать, какие порты используются на виртуальной Машине, выполните `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Чтобы загрузить виртуальный жесткий ДИСК Windows, который является контроллером домена:

   * Выполните [эти дополнительные шаги](https://support.microsoft.com/kb/2904015), чтобы подготовить диск.

   * Убедитесь, что вы знаете пароль режима восстановления служб каталогов (DSRM), в случае, если необходимо запустить виртуальную Машину в режиме DSRM в определенный момент. Дополнительные сведения см. в разделе [задайте пароль DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Убедитесь, что вы знаете имя встроенной учетной записи администратора и пароль. Вы можете сбросить текущий пароль локального администратора и убедитесь, что можно использовать эту учетную запись для входа в Windows через подключение удаленного рабочего СТОЛА. Это разрешение на доступ контролируется объектом «Локальный вход через службу удаленных рабочих столов» групповой политики. Просмотр этого объекта в редакторе локальных групповых политик здесь:

    Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment

8. Проверьте следующие политики Azure AD, чтобы убедиться в том, что доступ по протоколу RDP через протокол RDP или из сети не блокируются.

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Отказ в доступе к компьютеру из сети;

    - Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Запретить вход в систему через службу удаленных рабочих столов.


9. Проверьте следующие политики Azure AD, чтобы убедиться в том, что вы не удаляете любые учетные записи необходимый уровень доступа:

   - Компьютер Конфигурация компьютера\Параметры Settings\Security безопасности\Локальные политики\Назначение прав Assignment\Access этому компьютеру из сети

   Политики должны содержать следующие группы:

   - Администраторы

   - Операторы архивации

   - Все

   - Пользователи

10. Перезапустите виртуальную Машину, чтобы убедиться в том, что Windows по-прежнему работоспособна и могут быть достигнута через подключение удаленного рабочего СТОЛА. На этом этапе может потребоваться создать виртуальную Машину в вашей локальной Hyper-V убедитесь, что виртуальная машина будет полностью. Проверьте, чтобы убедиться в том, что имеет доступ к виртуальной Машине с помощью протокола удаленного рабочего СТОЛА.

11. Удалите все дополнительные фильтры интерфейса драйвера транспорта (TDI). Например, удалить программное обеспечение, которое анализирует TCP пакеты или дополнительные брандмауэры. Если вам нужно более поздней версии см. в этом, это можно сделать после развертывания виртуальной Машины в Azure.

12. Удалите все стороннее программное обеспечение или драйвер, связанных с физическими компонентов или любой другой технологии виртуализации.

### <a name="install-windows-updates"></a>Установка обновлений Windows
В идеале следует хранить компьютер в обновленном *уровень обновления*. Если это невозможно, убедитесь, что установлены следующие обновления:

| Компонент               | Бинарный         | Windows 7 с пакетом обновления 1, Windows Server 2008 R2 с пакетом обновления 1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | V1703 Windows 10    | V1709 Windows 10, Windows Server 2016 v1709 | V1803 Windows 10, Windows Server 2016 v1803 |
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
       
### Определение необходимости использования Sysprep <a id="step23"></a>    

Программа подготовки системы (Sysprep) — это процесс, можно запустить Сброс установки Windows. Интерфейс «стандартной» с помощью средства Sysprep, удаляя все персональные данные и сбрасывая некоторые компоненты. 

Обычно вы запустите Sysprep, чтобы создать шаблон, из которого можно развернуть несколько виртуальных машин с определенной конфигурацией. Этот шаблон вызывается *универсальным образом*.

Если вы хотите создать только одну виртуальную Машину с одного диска, не нужно использовать средство Sysprep. Вместо этого можно создать виртуальную Машину из *специализированного образа*. Сведения о том, как создать виртуальную Машину из специализированного диска см. в разделе:

- [Создание виртуальной машины Windows из специализированного диска](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master) (Создание виртуальной машины из специализированного VHD-диска)

Если вы хотите создать универсальный образ, необходимо запустить средство Sysprep. Дополнительные сведения см. в разделе [как использовать Sysprep: Общие сведения о](https://technet.microsoft.com/library/bb457073.aspx). 

Не все роли или приложение, устанавливаемое на компьютере под управлением Windows поддерживает обобщенную изображения. Поэтому перед выполнением этой процедуры, убедитесь, что Sysprep поддерживает роли компьютера. Дополнительные сведения см. в статье о [поддержке ролей сервера в Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Подготовка к использованию виртуального жесткого диска

>[!NOTE]
> После того, как `sysprep.exe` в следующих шагах, отключить виртуальную Машину. Не включайте ее снова, пока не создадите образ из него в Azure.

1. Войдите на виртуальную машину Windows.
1. Запустите **командную строку** от имени администратора. 
1. Перейдите в каталог `%windir%\system32\sysprep`. Затем выполните `sysprep.exe`.
1. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.

    ![Средство SysPrep](media/prepare-for-upload-vhd-image/syspre.png)
1. В разделе **Параметры завершения работы** выберите **Завершение работы**.
1. Нажмите кнопку **ОК**.
1. По завершении работы Sysprep выключите виртуальную Машину. Не используйте **перезапустите** завершить работу виртуальной Машины.

Теперь диск VHD можно отправлять. Дополнительные сведения о том, как создать виртуальную Машину из универсального образа см. в разделе [Отправка универсального диска VHD и использовать его для создания новой виртуальной Машины в Azure](sa-upload-generalized.md).


>[!NOTE]
> Пользовательский *unattend.xml* файл не поддерживается. Несмотря на то, что мы поддерживаем `additionalUnattendContent` свойство, которое предоставляет только ограниченная поддержка для добавления [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) параметры в *unattend.xml* файла, который Azure подготовки агент использует. Можно использовать, например, [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) Добавление FirstLogonCommands и LogonCommands. Дополнительные сведения см. в разделе [additionalUnattendContent FirstLogonCommands пример](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Выполните рекомендуемые настройки
Следующие параметры не влияют на передачу VHD. Тем не менее, мы настоятельно рекомендуем их настроить.

* Установка [агент виртуальной машины Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Затем можно включить расширения виртуальной машины. Расширения виртуальной Машины реализуют большую часть важных функций, может потребоваться при работе с виртуальными машинами. Вам потребуется расширения, например, для сброса паролей или настройки удаленного рабочего СТОЛА. Дополнительные сведения см. в разделе [Обзор агента виртуальной машины Azure](../extensions/agent-windows.md).
* После создания виртуальной Машины в Azure, мы рекомендуем размещать файл подкачки на *временного диска тома* для повышения производительности. Можно настроить размещение файлов следующим образом:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Диск данных, подключенного к виртуальной Машине, буква том временного диска обычно является *D*. Это назначение может отличаться в зависимости от настроек и количество дисков.

## <a name="next-steps"></a>Следующие шаги
* [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](upload-generalized-managed.md)
* [Устранение неполадок активации виртуальных Машин Windows Azure](troubleshoot-activation-problems.md)

