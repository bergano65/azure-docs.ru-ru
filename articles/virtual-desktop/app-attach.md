---
title: Windows Виртуальный рабочий стол MSIX приложение прикрепить - Azure
description: Как настроить приложение MSIX для Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128315"
---
# <a name="set-up-msix-app-attach"></a>Настройка присоединения приложения MSIX

> [!IMPORTANT]
> Присоединение приложения MSIX в настоящее время находится в открытом предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Эта тема будет вам пройти через как настроить приложение MSIX прикрепить в среде виртуального рабочего стола Windows.

## <a name="requirements"></a>Требования

Прежде чем приступить к работе, вот что вам нужно настроить приложение MSIX:

- Доступ к порталу Предварительного доступа Windows для получения версии Windows 10 с поддержкой приложения MSIX прикрепить AIS.
- Функционирование развертывания виртуального рабочего стола Windows. Для получения информации [см. Создать арендатора в Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Инструмент упаковки MSIX
- Доля сети в развертывании виртуального рабочего стола Windows, где будет храниться пакет MSIX

## <a name="get-the-os-image"></a>Получить изображение ОС

Во-первых, вам нужно получить изображение ОС, которые вы будете использовать для приложения MSIX. Чтобы получить изображение ОС:

1. Откройте [портал Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) и вопийте.

     >[!NOTE]
     >Вы должны быть членом программы предварительной оценки Windows для доступа к порталу Windows Insider. Чтобы узнать больше о программе предварительной оценки Windows, ознакомьтесь с нашей [инсайдерской документацией Windows.](/windows-insider/at-home/)

2. Прокрутите вниз в разделе **Выберите издание** и выберите **Windows 10 Инсайдерская Предварительный предприятие (FAST) - Построить 19035** или позже.

3. Выберите **Подтвердите,** затем выберите язык, который вы хотите использовать, а затем выберите **Подтвердите** еще раз.
    
     >[!NOTE]
     >На данный момент, английский является единственным языком, который был протестирован с функцией. Можно выбрать другие языки, но они могут отображаться не по назначению.
    
4. При сгенерировании ссылки загрузки выберите **64-битную загрузку** и сохраните ее на локальном жестком диске.

## <a name="prepare-the-vhd-image-for-azure"></a>Подготовка изображения VHD для Azure 

Прежде чем приступить к работе, необходимо создать изображение VHD-иное гостевого изображения. Если вы еще не создали свой мастер VHD изображение еще, перейдите [на подготовку и настроить изображение мастер VHD](set-up-customize-master-image.md) и следовать инструкциям там. 

После создания основного VHD-изображения необходимо отключить автоматические обновления для приложений приложения MSIX. Чтобы отключить автоматические обновления, необходимо запустить следующие команды в повышенной оперативной версии:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Затем подготовьте VM VHD для Azure и загрузите полученный диск VHD в Azure. Чтобы узнать больше, смотрите [Подготовка и настройка изображения мастера VHD.](set-up-customize-master-image.md)

После загрузки VHD в Azure создайте пул хоста, основанный на этом новом изображении, следуя инструкциям в [пуле хоста, используя учебник Azure Marketplace.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Подготовьте приложение для приложения MSIX 

Если у вас уже есть пакет MSIX, перейдите вперед, чтобы [настроить Windows Virtual Desktop инфраструктуры.](#configure-windows-virtual-desktop-infrastructure) Если вы хотите протестировать устаревшие приложения, следуйте инструкциям в [создании пакета MSIX от установщика рабочего стола на VM,](/windows/msix/packaging-tool/create-app-package-msi-vm/) чтобы преобразовать устаревавшую заявку в пакет MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Создание пакета VHD или VHDX для MSIX

Пакеты находятся в формате VHD или VHDX для оптимизации производительности. MSIX требует, чтобы пакеты VHD или VHDX работали должным образом.

Для создания пакета VHD или VHDX для MSIX:

1. [Загрузите инструмент msixmgr](https://aka.ms/msixmgr) и сохраните папку .zip в папку в vM-узле сеанса.

2. Расстегните папку msixmgr .zip.

3. Поместите исходный пакет MSIX в ту же папку, где вы расстегнули инструмент msixmgr.

4. Выполнить следующие cmdlet в PowerShell для создания VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Убедитесь, что размер VHD достаточно велик, чтобы удерживать расширенный MSIX».

5. Выполнить следующие cmdlet для установки вновь созданного VHD:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Запустите этот cmdlet для инициализации VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Выполнить этот cmdlet для создания новой перегородки:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Запустите этот cmdlet для формата раздела:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Создайте родительскую папку на установленном VHD. Этот шаг является обязательным, так как присоединение приложения MSIX требует родительской папки. Вы можете назвать родительскую папку, что угодно.

### <a name="expand-msix"></a>Расширить MSIX

После этого, вам нужно будет "расширить" изображение MSIX, распаковывая его. Распаковывать изображение MSIX:

1. Откройте запрос команды в качестве администратора и перейдите к папке, где вы скачали и расстегнулинструмент msixmgr.

2. Запустите следующий cmdlet, чтобы распаковать MSIX в VHD, который вы создали и смонтировали в предыдущем разделе.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Следующее сообщение должно появиться после того, как распаковка выполняется:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > При использовании пакетов из Microsoft Store for Business (или Education) в вашей сети или на устройствах, не подключенных к Интернету, вам необходимо получить лицензии пакетов в Магазине и установить их для успешного запуска приложения. Смотрите [Пакеты использования в автономном режиме](#use-packages-offline).

3. Перейдите к установленной VHD и откройте папку приложения и подтвердите содержимое пакета.

4. Отключите виртуальный жесткий диск.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Настройка инфраструктуры виртуального рабочего стола Windows

По замыслу один расширенный пакет MSIX (VHD, созданный в предыдущем разделе), можно разделить между несколькими vMs-мистами, поскольку VHD-режим ы прикрепляются в режиме только для чтения.

Перед запуском убедитесь, что ваша доля сети соответствует этим требованиям:

- Доля совместима с SMB.
- В ми-предправления, которые являются частью пула хостов сеансов, есть разрешения NTFS на долю.

### <a name="set-up-an-msix-app-attach-share"></a>Настройка доли присоединения приложения MSIX 

В среде Windows Virtual Desktop создайте сетевой обмен и переместите пакет туда.

>[!NOTE]
> Наилучшей практикой для создания сетевых акций MSIX является настройка доли сети с разрешениями NTFS только для чтения.

## <a name="install-certificates"></a>Установка сертификатов

Если ваше приложение использует сертификат, который не является достоверным или был подписан самостоятельно, вот как его установить:

1. Нажмите правой кнопкой мыши пакет и выберите **Свойства**.
2. В окне, которое появляется, выберите вкладку **Цифровые подписи.** На вкладке должен быть только один элемент, как показано на следующем изображении. Выберите этот элемент, чтобы выделить элемент, а затем выберите **Подробнее.**
3. При появляются данные окна цифровых подписей, выберите вкладку **«Общая»,** а затем выберите **сертификат «Установка».**
4. Когда установщик откроется, выберите **локальную машину** в качестве места хранения, а затем выберите **Next.**
5. Если установщик попросит вас разрешить приложению вносить изменения в устройство, выберите **«Да».**
6. Выберите **место все сертификаты в следующем магазине,** а затем выберите **Просмотр**.
7. При появляются окна магазина выбора сертификата, выберите **Доверенных людей,** а затем выберите **OK.**
8. Нажмите кнопку **Готово**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Подготовка скриптов PowerShell для присоединения приложения MSIX

Присоединение приложения MSIX состоит из четырех различных фаз, которые должны быть выполнены в следующем порядке:

1. Этап
2. Зарегистрировать
3. Отменить регистрацию
4. Дестадж

Каждый этап создает сценарий PowerShell. Примеры скриптов для каждого этапа доступны [здесь](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Этап сценария PowerShell

Перед обновлением скриптов PowerShell убедитесь, что у вас есть объем GUID тома в VHD. Чтобы получить объем GUID:

1.  Откройте сетевой раздел, где VHD находится внутри VM, где вы запустите скрипт.

2.  Справа щелкните VHD и выберите **гору**. Это будет монтировать VHD к приводу письмо.

3.  После установки VHD откроется окно **File Explorer.** Захват родительской папки и обновление **переменной $parentFolder**

    >[!NOTE]
    >Если вы не видите родительскую папку, это означает, что MSIX не был расширен должным образом. Повторите предыдущий раздел и повторите попытку.

4.  Откройте родительскую папку. Если правильно расширена, вы увидите папку с тем же именем, что и пакет. Обновление **$packageName** переменной, чтобы соответствовать названию этой папки.

    Например, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Откройте командный запрос и введите **mountvol.** Эта команда будет отображать список томов и их GUID. Копируйте GUID тома, в котором буква привода соответствует диску, на котором вы смонтировали VHD в шаге 2.

    Например, в этом примере выход для команды mountvol, если вы установили VHD на `C:\`Drive C, вы хотите скопировать значение выше:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Обновление **$volumeGuid** переменной с объемом GUID вы только что скопировали.

7. Откройте запрос Admin PowerShell и обновите следующий скрипт PowerShell с переменными, применимыми к вашей среде.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Регистрация скрипт PowerShell

Для запуска скрипта регистра запустите следующие cmdlets PowerShell со значениями заполнителя, замененными значениями, применимыми к вашей среде.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Снятие с регистрации скрипта PowerShell

Для этого скрипта замените заполнитель для **$packageName** с именем тестируемого пакета.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Сценарий Destage PowerShell

Для этого скрипта замените заполнитель для **$packageName** с именем тестируемого пакета.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Настройка сценариев моделирования для агента присоединения приложения MSIX

После создания скриптов пользователи могут вручную запускать их или настраивать для автоматического запуска в виде сценариев запуска, входа, входа и выключения. Чтобы узнать больше об этих типах скриптов, [см.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)

Каждый из этих автоматических скриптов выполняет один этап приложения прикрепить скрипты:

- Сценарий запуска запускает сценический сценарий.
- Скрипт входа выполняет сценарий регистра.
- Скрипт входа выполняет сценарий дерегистра.
- Скрипт выключения выполняет сценарий дестаза.

## <a name="use-packages-offline"></a>Использование пакетов в автономном режиме

Если вы используете пакеты из [Microsoft Store for Business](https://businessstore.microsoft.com/) или Microsoft Store for [Education](https://educationstore.microsoft.com/) в вашей сети или на устройствах, не подключенных к Интернету, вам необходимо получить лицензии пакетов из Microsoft Store и установить их на устройство для успешного запуска приложения. Если устройство находится в сети и может подключиться к Microsoft Store for Business, необходимые лицензии следует загружать автоматически, но если вы находитесь в автономном режиме, вам нужно настроить лицензии вручную. 

Для установки лицензионных файлов необходимо использовать скрипт PowerShell, который вызывает MDM_EnterpriseModernAppManagement_StoreLicenses02_01 класс в поставщике моста WMI.  

Вот как настроить лицензии для автономного использования: 

1. Загрузите пакет приложений, лицензии и необходимые платформы из Microsoft Store for Business. Вам нужны как закодированные, так и незакодированные файлы лицензии. Подробные инструкции по загрузке можно найти [здесь](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Обновление следующих переменных в скрипте для шага 3:
      1. `$contentID`— значение ContentID из некодированного лицензионного файла (.xml). Вы можете открыть файл лицензии в текстовом редакторе по вашему выбору.
      2. `$licenseBlob`— это вся строка для капли лицензии в закодированном файле лицензии (.bin). Вы можете открыть закодированный файл лицензии в текстовом редакторе по вашему выбору. 
3. Выполнить следующий сценарий из запроса Admin PowerShell. Хорошее место для выполнения установки лицензии находится в конце [сцены постановки,](#stage-the-powershell-script) который также должен быть запущен с запроса Admin.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Дальнейшие действия

Эта функция в настоящее время не поддерживается, но вы можете задать вопросы сообществу в [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Вы также можете оставить обратную связь для Windows Virtual Desktop в [центре обратной связи Windows Virtual Desktop](https://aka.ms/MRSFeedbackHub)или оставить обратную связь для приложения MSIX и упаковочного инструмента в [центре обратной связи MSIX](https://aka.ms/msixappattachfeedback) и [центре обратной связи с упаковочным инструментом MSIX.](https://aka.ms/msixtoolfeedback)
