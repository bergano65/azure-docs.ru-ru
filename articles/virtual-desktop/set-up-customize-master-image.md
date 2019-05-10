---
title: Подготовка и настройка главного образа виртуального жесткого диска — Azure
description: Как подготовить, настроить и передать главный образ предварительной версии виртуального рабочего стола Windows в Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 58471dc539f72c49b041638e928dda751f4bf5a2
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410593"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Подготовка и настройка главного образа VHD

В этой статье о том, как подготовить образ master виртуального жесткого диска (VHD) для передачи в Azure, в том числе о способах создания виртуальных машин (ВМ), установите и настройте программное обеспечение на них. Эти инструкции предназначены для конфигурации предварительного просмотра конкретных виртуального рабочего стола Windows, которая может использоваться с существующим процессам вашей организации.

## <a name="create-a-vm"></a>Создание виртуальной машины

Нескольких сеансов Windows 10 Корпоративная доступна в коллекции образов Azure. Существует два варианта для настройки этого образа.

Первый вариант — для подготовки виртуальной машины (VM) в Azure, следуя инструкциям в [Создание виртуальной Машины из управляемого образа](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)и перейдите к разделу [подготовки программного обеспечения и установки](set-up-customize-master-image.md#software-preparation-and-installation).

Второй вариант — создать образ локально, скачивание образа, подготовки виртуальной Машины Hyper-V и настройка его в соответствии с потребностями, которые будут рассмотрены в следующем разделе.

### <a name="local-image-creation"></a>Создание локального образа

После загрузки образа в локальной папке, откройте **диспетчера Hyper-V** создать виртуальную Машину с виртуальным жестким ДИСКОМ, был просто скопирован. Ниже приведен простой версии, но можно найти более подробные инструкции в [Создание виртуальной машины в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Чтобы создать виртуальную Машину с помощью скопированного виртуального жесткого диска:

1. Откройте **мастера создания виртуальной машины**.

2. На странице "укажите поколение" выберите **поколения 1**.

    ![Снимок экрана страницы укажите поколение. Выбран параметр «1-го поколения.](media/a41174fd41302a181e46385e1e701975.png)

3. В качестве типа контрольной точки отключите контрольные точки, сняв флажок.

    ![Снимок экрана раздела тип контрольной точки на странице контрольные точки.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Можно также выполните следующий командлет в PowerShell, чтобы отключить контрольные точки.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Фиксированный диск

При создании виртуальной Машины из существующего виртуального жесткого диска по умолчанию создает динамический диск. Его можно изменить диск с фиксированным размером, выбрав **изменить диск...**  как показано на следующем рисунке. Более подробные инструкции см. в разделе [Подготовка к отправке в Azure виртуального жесткого диска Windows или VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Снимок экрана с вариантом изменение диска.](media/35772414b5a0f81f06f54065561d1414.png)

Также можно выполнить следующий командлет PowerShell, чтобы изменить диск в фиксированный диск.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Подготовка программного обеспечения и установки

В этом разделе описывается Подготовка и установка Office 365 профессиональный плюс, OneDrive, FSLogix, Защитник Windows и другие распространенные приложения. Если пользователям требуется доступ к определенным бизнес-приложений, мы рекомендуем вам установить их после завершения инструкции в этом разделе.

В этом разделе предполагается, что расширенные права доступа на виртуальной Машине, ли он подготавливается в Azure или Hyper-V Manager.

### <a name="install-office-in-shared-computer-activation-mode"></a>Установка Office в общий режим активации компьютера

Используйте [средства развертывания Office](https://www.microsoft.com/download/details.aspx?id=49117) для установки Office. Windows 10 Корпоративная нескольких сеансов поддерживает только Office 365 профессиональный плюс, не Perpetual Office 2019 г.

Средство развертывания Office требуется файл конфигурации XML. Чтобы настроить в следующем примере, см. в разделе [параметры конфигурации для средства развертывания Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Этот пример конфигурации XML, мы предоставили сделает следующее:

- Установите Office из канала участников программы предварительной оценки и доставки обновлений из канала сотрудники компании при их исполнения.
- Архитектура использования x64.
- Отключите автоматическое обновление.
- Установка Visio и Project.
- Удалите все существующие установки Office и переноса их параметров.
- Включение общих компьютерах, лицензирование для операции в среде сервера терминалов.

Вот, это пример XML-ФАЙЛ конфигурации, которые не:

- Установка Скайп для бизнеса
- Установка OneDrive в режиме пользователя. Дополнительные сведения см. в разделе [установить OneDrive в режиме на уровне компьютера](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Лицензирование общего компьютера можно настроить через объекты групповой политики (GPO) или параметры реестра. Объект групповой Политики расположен в **Конфигурация компьютера\\политики\\административные шаблоны\\Microsoft Office 2016 (компьютер)\\параметры лицензирования**

Средство развертывания Office содержит setup.exe. Чтобы установить Office, выполните следующую команду в командной строке:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Пример configuration.xml

В следующем примере XML установит выпуск программы предварительной оценки, также известное как участников программы предварительной оценки Fast или Main участников программы предварительной оценки.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Группа разработчиков Office рекомендуется использовать 64-разрядной установки для **OfficeClientEdition** параметра.

После установки Office, вы можете обновить Office по умолчанию. Выполните следующие команды по отдельности или в пакетном файле обновлять поведение.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

### <a name="disable-automatic-updates"></a>Отключить автоматическое обновление

Чтобы отключить автоматическое обновление с помощью групповой политики:

1. Откройте **редактор локальных групповых политик\\административные шаблоны\\компоненты Windows\\Windows Update**.
2. Щелкните правой кнопкой мыши **настроить автоматическое обновление** и присвойте ему значение **отключено**.

Можно также выполните следующую команду в командной строке, чтобы отключить автоматическое обновление.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Задать макет Start для ПК Windows 10 (необязательно)

Выполните следующую команду, чтобы указать макет начального для компьютеров с Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Установка OneDrive в режиме на уровне компьютера

OneDrive — обычно устанавливается для каждого пользователя. В этой среде, он должен быть установлен на компьютере.

Вот как можно установить OneDrive в режиме на уровне компьютера:

1. Во-первых создайте расположение для размещения установщик OneDrive. Папку на локальном диске или [\\\\unc] подходит расположение (file://unc).

2. Скачайте OneDriveSetup.exe в промежуточное расположение с помощью этой ссылки: <https://aka.ms/OneDriveWVD-Installer>

3. Если вы установили office с OneDrive, опустив  **\<ExcludeApp ID = «OneDrive» /\>**, удалите все существующие установки пользователя OneDrive из командной строки с повышенными правами, выполнив следующую команда:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Выполните следующую команду из командной строки с повышенными, чтобы задать **AllUsersInstall** значение реестра:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Выполните следующую команду, чтобы установить OneDrive в режиме на уровне компьютера:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Выполните следующую команду для настройки OneDrive для запуска при входе в систему для всех пользователей:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Включить **Автоматическая настройка учетной записи пользователя** , выполнив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Перенаправление и переместите Windows известные папки в OneDrive, выполнив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>Команды и Скайп

Виртуальный рабочий стол Windows не поддерживает официально Скайп для бизнеса и команд.

### <a name="set-up-user-profile-container-fslogix"></a>Настроить контейнер профиля пользователя (FSLogix)

Чтобы включить контейнер FSLogix как часть изображения, следуйте инструкциям в [Настройка общей папки профиля пользователя в пуле узлов](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Можно проверить функциональные возможности FSLogix контейнера с [в этом кратком руководстве](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Настроить Защитник Windows

Если Защитник Windows настроен на виртуальной машине, убедитесь, что он настроен для проверки не все содержимое файлов VHD и VHDX при вложении одной и той же.

Эта конфигурация только удаляет сканирование VHD и vhdx-файлов во время вложение, но не влияет на сканирование в реальном времени.

Более подробные инструкции по настройке Защитника Windows в Windows Server, см. в разделе [исключения Настройка антивирусной программы Защитника Windows в Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Дополнительные сведения о том, как настроить Защитник Windows, чтобы исключить определенные файлы из проверки, см. в разделе [Настройка и проверка на основе расположения файла расширения и папку исключения](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Настройка политик времени ожидания сеанса

Удаленный сеанс политики могут применяться на уровне групповой политики, так как все виртуальные машины в пуле узлов входят в ту же группу безопасности.

Настройка политики удаленного сеанса.

1. Перейдите к **административные шаблоны** > **компоненты Windows** > **служб удаленных рабочих столов**  >  **Узла сеансов удаленных рабочих столов** > **ограничений по времени сеанса**.
2. На панели справа выберите **установленного лимита времени для бездействующих сеансов служб удаленных рабочих столов** политики.
3. После того как появится модальное окно, измените параметр политики из **не настроен** для **включено** для активации политики.
4. В раскрывающемся меню под параметр политики, установка интервала времени для **4 часа**.

Удаленный сеанс политики также можно настроить вручную, выполнив следующие команды:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Настройка перенаправления часового пояса

Перенаправление часового пояса может применяться на уровне групповой политики, так как все виртуальные машины в пуле узлов входят в ту же группу безопасности.

Чтобы перенаправить часовых поясов:

1. На сервере Active Directory, откройте **консоли управления групповыми политиками**.
2. Разверните домен и объекты групповой политики.
3. Щелкните правой кнопкой мыши **объекта групповой политики** , созданную для параметров групповой политики и выберите **изменить**.
4. В **редактор управления групповыми политиками**, перейдите к **Конфигурация компьютера** > **политики** > **администрирования Шаблоны** > **компоненты Windows** > **служб удаленных рабочих столов** > **узла сеансов удаленных рабочих столов**   >  **Перенаправление устройств и ресурсов**.
5. Включить **разрешить функцию перенаправления часового пояса** параметр.

Эту команду можно также выполнить на основной образ для перенаправления часовых поясов:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Отключить контроль памяти

Для узла сеансов виртуального рабочего стола Windows, Windows 10 Корпоративная или Windows 10 Корпоративная нескольких сеансов мы рекомендуем отключить контроль памяти. Контроль памяти можно отключить в меню "Параметры" в разделе **хранения**, как показано на следующем снимке экрана:

![Снимок экрана меню хранилища в разделе параметров. Параметр «Контроль памяти» будет отключено.](media/storagesense.png)

Можно также изменить параметр в реестре, выполнив следующую команду:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Включают поддержку дополнительных языков

В этой статье не рассматривается настройка языковой и региональной поддержки. Дополнительные сведения см. в следующих статьях:

- [Добавление языков в образы Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Компоненты по требованию](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Язык и регион компоненты по запросу (FOD)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Другие приложения и конфигурации реестра

В этом разделе рассматриваются приложения и конфигурации операционной системы. В этом разделе все конфигурации осуществляется с помощью записи реестра, которые могут быть выполнены с командной строки и средства regedit.

>[!NOTE]
>Вы можете реализовать рекомендации в конфигурации с Общие политики объекты групповой политики или реестра imports. Администратор может выбрать любой из вариантов, в соответствии с требованиями их организации.

Для отзыва центра сбор данных телеметрии на нескольких сеансов Windows 10 Корпоративная выполните следующую команду:

```batch
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Выполните следующую команду, чтобы устранить сбои "Доктор Ватсон":

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Введите следующие команды в редактор реестра, чтобы исправить Техническая поддержка 5 k. Перед включением side-by-side стека, необходимо выполнить команды.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Подготовка образа для передачи в Azure

После завершения настройки и установки всех приложений, следуйте инструкциям в [Подготовка к отправке в Azure виртуального жесткого диска Windows или VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) для подготовки образа.

После подготовки образа для передачи, убедитесь, что виртуальная машина остается в состоянии off или высвобождены.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Отправьте главный образ в учетную запись хранилища в Azure

Этот раздел применим только в том случае, когда главный образ была создана локально.

Приведенные ниже инструкции о том, как передать ваш главный образ в учетную запись хранилища Azure. Если у вас нет учетной записи хранения Azure, следуйте инструкциям в [в этой статье](https://code.visualstudio.com/tutorials/static-website/create-storage) для ее создания.

1. Преобразуйте в фиксированный образа виртуальной Машины (VHD), если у вас еще нет. Если вы не преобразуете изображение фиксированный, невозможно создать образ.

2. Передать VHD в контейнер больших двоичных объектов в учетной записи хранения. Вы можете быстро отправить с [средства обозревателя хранилища](https://azure.microsoft.com/features/storage-explorer/). Дополнительные сведения о средстве обозреватель службы хранилища, см. в разделе [в этой статье](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Снимок экрана Microsoft Azure средство обозревателя хранилищ в окне поиска. Установлен флажок «Отправить VHD- или vhdx-файлы как страничные (рекомендуется)».](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Теперь перейдите на портал Azure в браузере и поиск «Images». Поиск должен привести к **созданию образа** странице, как показано на следующем снимке экрана:

    ![Снимок экрана для создания изображения страницы портала Azure, заполненный примеры значений для образа.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. После создания образа, вы увидите уведомление, как показано на следующем снимке экрана:

    ![Снимок экрана уведомление «успешно создан образ».](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть образ, можно создать или обновить пулы узлов. Дополнительные сведения о том, как создавать и обновлять пулы узлов, см. в разделе со следующими статьями:

- [Создание пула узлов с помощью шаблона Azure Resource Manager](create-host-pools-arm-template.md)
- [Учебник. Создание пула узла с помощью Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Создание пула узла с помощью PowerShell](create-host-pools-powershell.md)
- [Настройка общей папки профиля пользователя в пуле узлов](create-host-pools-user-profile.md)
- [Настройка метода балансировки нагрузки виртуальный рабочий стол Windows](configure-host-pool-load-balancing.md)
