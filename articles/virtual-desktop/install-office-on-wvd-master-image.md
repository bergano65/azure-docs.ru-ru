---
title: Установите Office на главный образ виртуального жесткого диска — Azure
description: Как установить и настроить Office на master изображение предварительного просмотра виртуального рабочего стола Windows Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 6d2bb7efdd5567da377e1e15fec4935b7d4a3a6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444118"
---
# <a name="install-office-on-a-master-vhd-image"></a>Установка Office в главный образ VHD

В этой статье рассказывается, как установить Office 365 профессиональный плюс, OneDrive и другие распространенные приложения на основе образа master виртуального жесткого диска (VHD) для передачи в Azure. Если пользователям требуется доступ к определенной строки бизнес-приложений, мы рекомендуем установить их после выполнения указаний в этой статье.

В этой статье предполагается, что вы уже создали виртуальную машину (ВМ). Если это не так, см. в разделе [Подготовка и настройка главного образа виртуального жесткого диска](set-up-customize-master-image.md#create-a-vm)

В этой статье также предполагается, что расширенные права доступа на виртуальной Машине, является ли он подготавливается в Azure или Hyper-V Manager. Если это не так, см. в разделе [повышение прав доступа для управления всеми группами Azure подписки и управления](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Эти инструкции предназначены для конфигурации предварительного просмотра конкретных виртуального рабочего стола Windows, которая может использоваться с существующим процессам вашей организации.

## <a name="install-office-in-shared-computer-activation-mode"></a>Установка Office в общий режим активации компьютера

Активацию на общем компьютере позволяет развернуть Office 365 профессиональный плюс на компьютере в вашей организации, к которому осуществляется несколькими пользователями. Дополнительные сведения об активации на общем компьютере см. в разделе [Обзор активации на общем компьютере для Office 365 профессиональный плюс](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Используйте [средства развертывания Office](https://www.microsoft.com/download/details.aspx?id=49117) для установки Office. Windows 10 Корпоративная нескольких сеансов поддерживает только следующие версии Office:
- Office 365 профессиональный плюс.
- Office 365 для бизнеса поставляемом с подпиской Microsoft 365 бизнес

Средство развертывания Office требуется файл конфигурации XML. Чтобы настроить в следующем примере, см. в разделе [параметры конфигурации для средства развертывания Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Этот пример конфигурации XML, мы предоставили сделает следующее:

- Установите Office из канала участников программы предварительной оценки и доставки обновлений из канала сотрудники компании при их исполнения.
- Архитектура использования x64.
- Отключите автоматическое обновление.
- Установка Visio и Project.
- Удалите все существующие установки Office и переноса их параметров.
- Включите активацию на общем компьютере.

>[!NOTE]
>Функции поиска шаблона в Visio не работает в виртуальный рабочий стол Windows во время настройки предварительной версии.

Вот, это пример XML-ФАЙЛ конфигурации, которые не:

- Установка Скайп для бизнеса
- Установка OneDrive в режиме пользователя. Дополнительные сведения см. в разделе [установить OneDrive в режиме на уровне компьютера](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Общий активации компьютера можно настроить через объекты групповой политики (GPO) или параметры реестра. Объект групповой Политики расположен в **Конфигурация компьютера\\политики\\административные шаблоны\\Microsoft Office 2016 (компьютер)\\параметры лицензирования**

Средство развертывания Office содержит setup.exe. Чтобы установить Office, выполните следующую команду в командной строке:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Пример configuration.xml

В следующем примере XML установит выпуск программы предварительной оценки.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Установка OneDrive в режиме на уровне компьютера

OneDrive — обычно устанавливается для каждого пользователя. В этой среде, он должен быть установлен на компьютере.

Вот как можно установить OneDrive в режиме на уровне компьютера:

1. Во-первых создайте расположение для размещения установщик OneDrive. Папку на локальном диске или [\\\\unc] подходит расположение (file://unc).

2. Скачайте OneDriveSetup.exe в промежуточное расположение с помощью этой ссылки: <https://aka.ms/OneDriveWVD-Installer>

3. Если вы установили office с OneDrive, опустив  **\<ExcludeApp ID = «OneDrive» /\>** , удалите все существующие установки пользователя OneDrive из командной строки с повышенными правами, выполнив следующую команда:
    
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

## <a name="teams-and-skype"></a>Команды и Скайп

Виртуальный рабочий стол Windows не поддерживает Скайп для бизнеса и команд.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы добавили Office в образ, вы можете настроить главный образ виртуального жесткого диска. См. в разделе [Подготовка и настройка главного образа виртуального жесткого диска](set-up-customize-master-image.md).
