---
title: Установка Office на изображение мастера VHD - Azure
description: Как установить и настроить Office на главном изображении виртуального рабочего стола Windows в Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127852"
---
# <a name="install-office-on-a-master-vhd-image"></a>Установка Office в главный образ VHD

В этой статье рассказывается, как установить Office 365 ProPlus, OneDrive и другие распространенные приложения на изображение виртуального виртуального жесткого диска (VHD) для загрузки в Azure. Если пользователям необходимо получить доступ к определенным бизнес-приложениям (LOB), мы рекомендуем вам установить их после выполнения инструкций в этой статье.

Эта статья предполагает, что вы уже создали виртуальную машину (VM). Если нет, просмотрите [Подготовьте и настройте изображение мастера VHD](set-up-customize-master-image.md#create-a-vm)

В этой статье также предполагается, что у вас есть повышенный доступ к VM, будь то в Azure или Hyper-V Manager. В противном [Elevate access to manage all Azure subscription and management groups](../role-based-access-control/elevate-access-global-admin.md)случае см.

>[!NOTE]
>Эти инструкции предназначены для конфигурации Виртуального рабочего стола Windows, которую можно использовать в имеющихся процессах вашей организации.

## <a name="install-office-in-shared-computer-activation-mode"></a>Установка Office в режиме общей активации компьютера

Общая активация компьютера позволяет развертывать Office 365 ProPlus на компьютер в вашей организации, доступ к которым доступны нескольким пользователям. Для получения дополнительной информации об общей активации компьютера, смотрите [Обзор общей активации компьютера для Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Используйте [инструмент развертывания Office](https://www.microsoft.com/download/details.aspx?id=49117) для установки Office. Многосессионная Windows 10 Enterprise поддерживает только следующие версии Office:
- Office 365 профессиональный плюс.
- Office 365 Бизнес, который поставляется с подпиской Microsoft 365 Бизнес

Инструмент развертывания Office требует конфигурации xML-файла. Чтобы настроить следующий пример, [Configuration Options for the Office Deployment Tool](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)см.

Эта примерконфигурация XML, которая, как мы предоставили, будет выполнять следующие вещи:

- Установите Office из ежемесячного канала и доставьте обновления с ежемесячного канала, когда они будут выполнены.
- Используйте архитектуру x64.
- Отменяйте автоматические обновления.
- Удалите все существующие установки Office и переносите их настройки.
- Включить общую активацию компьютера.

>[!NOTE]
>Функция поиска трафаретов Visio может работать не так, как ожидалось в Windows Virtual Desktop.

Вот что эта конфигурация образца XML не будет делать:

- Установка Skype для бизнеса
- Установите OneDrive в режиме для пользователей. Чтобы узнать больше, смотрите [Установить OneDrive в режиме на машину.](#install-onedrive-in-per-machine-mode)

>[!NOTE]
>Общая активация компьютера может быть настроена через объекты групповой политики (ГПО) или настройки реестра. GPO находится в **настройках управления политиками компьютерной конфигурации\\\\\\Microsoft\\Office 2016 (Machine) Licensing Settings**

Инструмент развертывания Office содержит setup.exe. Чтобы установить Office, запустите следующую команду в командной строке:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Пример конфигурации.xml

Следующий образец XML установит ежемесячный выпуск.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Команда Office рекомендует использовать 64-битную установку для параметра **OfficeClientEdition.**

После установки Office можно обновить поведение Office по умолчанию. Выполнить следующие команды по отдельности или в пакетном файле для обновления поведения.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Установка OneDrive в режиме для одного автомата

OneDrive обычно устанавливается на пользователя. В этой среде он должен быть установлен на машину.

Вот как установить OneDrive в режиме на машину:

1. Во-первых, создать место для постановки установки OneDrive. Локальная папка диска или расположение «unc»\\\\(file://unc) в порядке.

2. Скачать OneDriveSetup.exe на ваше поэтапное местоположение по этой ссылке:<https://aka.ms/OneDriveWVD-Installer>

3. Если вы установили офис с OneDrive, опустив ** \<ExcludeApp\>ID"OneDrive" /**, удалите любые существующие установки OneDrive на пользователя из повышенной команды, запустив следующую команду:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Выполнить эту команду из поднятой команды запрос аранжировать значение реестра **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Запустите эту команду для установки OneDrive в режиме на одну машину:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Запустите эту команду, чтобы настроить OneDrive, чтобы начать при входном введем для всех пользователей:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Включить **Silently настроить учетную запись пользователя,** запустив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Перенаправьте и переместите известные папки Windows на OneDrive, запустив следующую команду.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Команды и Skype

Windows Virtual Desktop не поддерживает Skype для бизнеса и команд.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы добавили Office к изображению, вы можете продолжить настройку вашего основного VHD-изображения. Смотрите [Подготовка и настройка изображения мастера VHD.](set-up-customize-master-image.md)
