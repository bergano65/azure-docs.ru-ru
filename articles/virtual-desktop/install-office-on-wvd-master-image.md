---
title: Установка Office на главном образе VHD в Azure
description: Как установить и настроить Office в основном образе Windows для предварительного просмотра виртуальных рабочих столов в Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 79fe541d1bb3bea8447cf095673111362cec74d2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816434"
---
# <a name="install-office-on-a-master-vhd-image"></a>Установка Office в главный образ VHD

В этой статье описывается, как установить Office 365 ProPlus, OneDrive и другие общие приложения на главном образе виртуального жесткого диска (VHD) для отправки в Azure. Если пользователям требуется доступ к определенным бизнес-приложениям, рекомендуется установить их после выполнения инструкций, описанных в этой статье.

В этой статье предполагается, что вы уже создали виртуальную машину (ВМ). Если нет, см. статью [Подготовка и настройка главного образа VHD](set-up-customize-master-image.md#create-a-vm) .

Кроме того, в этой статье предполагается, что у вас есть повышенный доступ к виртуальной машине, будь то ее подготовка в Azure или диспетчере Hyper-V. Если нет, см. статью [повышение уровня доступа для управления всеми подписками Azure и группами управления](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Эти инструкции предназначены для конфигурации предварительной версии Виртуального рабочего стола Windows, которую можно использовать в имеющихся процессах вашей организации.

## <a name="install-office-in-shared-computer-activation-mode"></a>Установить Office в режиме активации общего компьютера

Активация на общем компьютере позволяет развернуть Office 365 профессиональный плюс на компьютере в Организации, к которому обращается несколько пользователей. Дополнительные сведения об активации общего компьютера см. в разделе [Обзор активации общего компьютера для Office 365 профессиональный плюс](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Используйте [средство развертывания Office](https://www.microsoft.com/download/details.aspx?id=49117) для установки Office. Многосеансовая поддержка Windows 10 Enterprise поддерживает только следующие версии Office:
- Office 365 профессиональный плюс.
- Office 365 Business, входящий в состав подписки Microsoft 365 бизнес

Средству развертывания Office требуется XML-файл конфигурации. Чтобы настроить следующий пример, см. раздел [Параметры конфигурации средства развертывания Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Этот пример XML-файла конфигурации, который мы предоставили, выполняет следующие действия:

- Установите Office из канала предварительной оценки и доставьте обновления из канала предварительной оценки при их выполнении.
- Используйте архитектуру x64.
- Отключение автоматического обновления.
- Установите Visio и Project.
- Удалите все существующие установки Office и перенесите их параметры.
- Включите активацию общего компьютера.

>[!NOTE]
>Функция поиска набора элементов в Visio не работает в виртуальной рабочем столе Windows во время предварительной конфигурации.

Ниже приведен пример XML-файла конфигурации.

- Установка Skype для бизнеса
- Установите OneDrive в режиме "на пользователя". Дополнительные сведения см. в статье [Установка OneDrive в режиме "на компьютер](#install-onedrive-in-per-machine-mode)".

>[!NOTE]
>Активацию на общем компьютере можно настроить с помощью групповая политика объектов (GPO) или параметров реестра. Объект GPO расположен в папке **"политики\\\\конфигурации компьютера\\" Административные шаблоны параметры лицензирования Microsoft Office\\2016 (компьютер)** .

Средство развертывания Office содержит файл Setup. exe. Чтобы установить Office, выполните в командной строке следующую команду:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Пример файла Configuration. XML

В следующем образце XML будет установлен выпуск для участников программы предварительной оценки.

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
>Команда разработчиков Office рекомендует использовать 64-разрядную установку для параметра **оффицеклиентедитион** .

После установки Office можно обновить поведение Office по умолчанию. Выполните следующие команды по отдельности или в пакетном файле, чтобы обновить поведение.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Установка OneDrive в режиме "на компьютер"

Как правило, OneDrive устанавливается отдельно для каждого пользователя. В этой среде она должна быть установлена для каждого компьютера.

Вот как можно установить OneDrive в режиме "на компьютер":

1. Сначала создайте расположение для размещения установщика OneDrive. Папка локального диска или [\\\\UNC] (file://UNC) — это нормально.

2. Скачайте Онедривесетуп. exe в промежуточное расположение с помощью этой ссылки:<https://aka.ms/OneDriveWVD-Installer>

3. Если вы установили Office в onedrive, опустив  **\<ексклудеапп ID = "OneDrive"/\>** , удалите все существующие установки onedrive для каждого пользователя из командной строки с повышенными привилегиями, выполнив следующую команду:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Выполните эту команду из командной строки с повышенными привилегиями, чтобы задать значение реестра **аллусерсинсталл** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Выполните следующую команду, чтобы установить OneDrive в режиме "на компьютер":

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Выполните следующую команду, чтобы настроить OneDrive для запуска при входе для всех пользователей:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Включите **автоматическую настройку учетной записи пользователя** , выполнив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Перенаправьте и переместите известные папки Windows в OneDrive, выполнив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Команды и Skype

Виртуальный рабочий стол Windows не поддерживает Skype для бизнеса и команды.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы добавили Office в образ, вы можете продолжить настройку главного образа VHD. См. раздел [Подготовка и настройка главного образа VHD](set-up-customize-master-image.md).
