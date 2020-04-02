---
title: Руководство по настройке шаблона Windows машины (ru) Документы Майкрософт
description: Общие шаги по подготовке шаблона Windows в лабораторных службах.  Эти шаги включают настройку расписания обновления Windows, установку OneDrive и установку Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521188"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Руководство по настройке шаблона Windows в лабораторных службах Azure

Если вы настраиваете шаблонwindows Windows 10 для лабораторных служб Azure, вот некоторые рекомендации и советы, которые необходимо рассмотреть. Шаги конфигурации ниже все необязательны.  Однако эти подготовительные шаги могут помочь сделать ваших студентов более продуктивными, свести к минимуму перерывы в работе занятий и убедиться, что они используют новейшие технологии.

>[!IMPORTANT]
>Эта статья содержит фрагменты PowerShell для оптимизации процесса изменения шаблона машины.  Для всех отображаемых скриптов PowerShell необходимо запустить их в Windows PowerShell с привилегиями администратора. В Windows 10, быстрый способ сделать это, чтобы право нажать меню Пуск и выбрать "Windows PowerShell (Админ)".

## <a name="install-and-configure-onedrive"></a>Установка и настройка OneDrive

Чтобы защитить данные учащихся от потери при сходе виртуальной машины, мы рекомендуем учащимся вернуть свои данные в облако.  Microsoft OneDrive может помочь студентам защитить свои данные.  

### <a name="install-onedrive"></a>Установка OneDrive

Чтобы вручную загрузить и установить OneDrive, смотрите [страницы загрузки OneDrive](https://onedrive.live.com/about/download/) или [OneDrive для бизнеса.](https://onedrive.live.com/about/business/)

Вы также можете использовать следующий скрипт PowerShell.  Он будет автоматически загружать и устанавливать последнюю версию OneDrive.  После установки клиента OneDrive запустите установщик.  В нашем примере `/allUsers` мы используем переключатель для установки OneDrive для всех пользователей на машине. Мы также `/silent` используем переключатель для бесшумной установки OneDrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Настройки OneDrive

Есть много [настроек, которые можно сделать для OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Давайте рассмотрим некоторые из наиболее распространенных настроек.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Тихо переместить Windows известных папок oneDrive

Складные материалы, такие как Документы, Загрузки и Картинки, часто используются для хранения студенческих файлов. Чтобы убедиться, что эти папки будут резервироваться в OneDrive, мы рекомендуем перенести эти папки на OneDrive.

Если вы находитесь на машине, которая не использует Active Directory, пользователи могут вручную переместить эти папки в OneDrive, как только они проходят проверку подлинности на OneDrive.

1. Открытие проводника
2. Нажмите правой кнопкой мыши папку «Документы, загрузки» или «Картинки».
3. Перейти к Свойства > Местоположение.  Переместите папку в новую папку в каталоге OneDrive.

Если ваша виртуальная машина подключена к Active Directory, вы можете настроить шаблонную машину, чтобы автоматически побудить студентов переместить известные папки в OneDrive.  

Сначала необходимо получить идентификатор арендатора Office.  Для получения дополнительных инструкций можно [найти Office 365 Tenant ID.](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)  Вы также можете получить Идентификатор клиента Office 365, используя следующий PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

После того, как у вас есть Office 365 Tenant ID, установите OneDrive, чтобы побудить переместить известные папки на OneDrive, используя следующие PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Используйте файлы OneDrive по запросу

Учащиеся могут иметь много файлов в своих учетных записях OneDrive. Чтобы сэкономить место на машине и сократить время загрузки, мы рекомендуем сделать все файлы, хранящиеся в аккаунте OneDrive студента, по требованию.  Файлы по требованию загружают только после того, как пользователь получает доступ к файлу.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Тихо войти в пользователей oneDrive

OneDrive может быть установлен для автоматического входа в систему с учетными данными Windows зарегистрированных на пользователя.  Автоматический входе в систему полезен для занятий, где студент вписывается в школьные учетные данные Office 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Отключить учебник, который появляется в конце установки OneDrive

Эта настройка позволяет предотвратить запуск учебника в веб-браузере в конце настройки OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Установите максимальный размер файла, который будет загружен автоматически

Эта настройка используется в сочетании с Silently подписать в пользователях OneDrive синхронизации клиента с их учетными данными Windows на устройствах, которые не имеют OneDrive файлов по требованию включен. Любому пользователю, у которого есть OneDrive, который больше указанного порога (в МБ), будет предложено выбрать папки, которые они хотят синхронизировать до того, как клиент синхронизации OneDrive (OneDrive.exe) загрузит файлы.  В нашем примере "1111-2222-3333-4444" - это Office 365 tenant ID и 0005000 устанавливает порог в 5 ГБ.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Установка и настройка Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Установка Microsoft Office 365

Если машина шаблона нуждается в Office, мы рекомендуем установить Office через [инструмент развертывания Office (ODT).](https://www.microsoft.com/download/details.aspx?id=49117 ) Вам нужно будет создать многоразовый файл конфигурации с помощью [службы конфигурации клиентов Office 365,](https://config.office.com/) чтобы выбрать, какая архитектура, какие функции вам понадобятся от Office, и как часто он обновляется.

1. Перейдите в [службу конфигурации клиентов Office 365](https://config.office.com/) и загрузите свой собственный файл конфигурации.
2. Скачать [инструмент развертывания Office](https://www.microsoft.com/download/details.aspx?id=49117).  Загруженный файл `setup.exe`будет .
3. Выполнить `setup.exe /download configuration.xml` для загрузки компонентов Office.
4. Запуск `setup.exe /configure configuration.xml` для установки компонентов Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Изменение канала обновления Microsoft Office 365

Используя инструмент конфигурации Office, можно установить, как часто Office получает обновления. Однако, если вам нужно изменить, как часто Office получает обновления после установки, вы можете изменить URL канала обновления. Url-адреса канала обновления можно найти в [канале «Изменить office 365 ProPlus» для устройств в вашей организации.](https://docs.microsoft.com/deployoffice/change-update-channels) Приведенный ниже пример показывает, как установить Office 365 для использования ежемесячного канала обновления.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Установка и настройка обновлений

### <a name="install-the-latest-windows-updates"></a>Установите последние обновления Windows

Мы рекомендуем установить последние обновления Microsoft на шаблон машины для целей безопасности, прежде чем публиковать шаблон VM.  Это также потенциально позволяет избежать нарушения учащимся в их работе, когда обновления заходят в неожиданное время.

1. **Настройки** запуска из меню «Пуск»
2. Нажмите на **обновление** & безопасности
3. Нажмите **Проверить наличие обновлений**
4. Обновления будут загружаться и устанавливаться.

Вы также можете использовать PowerShell для обновления шаблона машины.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Некоторые обновления могут потребовать перезапуска машины.  Если потребуется перезагрузка, вам будет предложено перезагрузиться.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Установка последних обновлений для приложений Microsoft Store

Мы рекомендуем, чтобы все приложения Microsoft Store были обновлены до их последних версий.  Ниже приведены инструкции по ручному обновлению приложений из Microsoft Store.  

1. Запуск приложения **Microsoft Store.**
2. Нажмите на эллипс (...) рядом с фотографией пользователя в верхнем углу приложения.
3. Выберите **Скачать** и обновления из выпадающего меню.
4. Нажмите **Кнопка обновления.**

Вы также можете использовать PowerShell для обновления уже установленных приложений Microsoft Store.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Остановить автоматические обновления Windows

После обновления Windows до последней версии можно было бы рассмотреть возможность остановки обновлений Windows.  Автоматические обновления потенциально могут повлиять на запланированное время занятий.  Если ваш курс работает дольше, попросите студентов вручную проверить наличие обновлений или настроить автоматические обновления на время, не стандартное расписание занятий.  Для получения дополнительной информации о настройках [manage additional Windows Update settings](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)для обновления Windows см.

Автоматические обновления Windows могут быть остановлены с помощью следующего скрипта PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Установка пакетов иностранного языка

Если вам нужны дополнительные языки, установленные на виртуальной машине, вы можете добавить их через Microsoft Store.

1. Запуск Microsoft Store
2. Поиск "языковой пакет"
3. Выберите язык для установки

Если вы уже вошли в шаблон VM, используйте [ярлык "Установить языковой пакет",](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) чтобы перейти непосредственно к соответствующей странице настроек.

## <a name="remove-unneeded-built-in-apps"></a>Удаление ненужных встроенных приложений

Windows 10 поставляется со многими встроенными приложениями, которые могут быть не нужны для вашего конкретного класса. Чтобы упростить изображение машины для студентов, можно удалить некоторые приложения из шаблонной машины.  Чтобы увидеть список установленных приложений, используйте смдлет PowerShell. `Get-AppxPackage`  В приведенном ниже примере показаны все установленные приложения, которые могут быть удалены.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Чтобы удалить приложение, используйте Смдлет Remove-Appx.  Приведенный ниже пример показывает, как удалить все, что связано с XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Установка общих приложений, связанных с обучением

Установите другие приложения, обычно используемые для обучения через приложение Магазина Windows. Предложения включают приложения, такие как [приложение Microsoft Whiteboard,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)и [Minecraft Education Edition.](https://education.minecraft.net/) Эти приложения должны устанавливаться вручную через Магазин Windows или через соответствующие веб-сайты на шаблоне VM.

## <a name="conclusion"></a>Заключение

Эта статья показала вам дополнительные шаги для подготовки шаблона Windows VM для эффективного класса.  Шаги включают установку OneDrive и установку Office 365, установку обновлений для Windows и установку обновлений для приложений Microsoft Store.  Мы также обсудили, как настроить обновления в расписании, которое лучше всего подходит для вашего класса.  

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со статьей о том, как контролировать поведение отключения Windows, чтобы помочь с управлением затратами: [Руководство по управлению поведением отключения Windows](how-to-windows-shutdown.md)
