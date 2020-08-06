---
title: Подключение приложения MSIX для виртуального рабочего стола Windows — Azure
description: Настройка подключения приложения MSIX для виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 328f7bb8c03cb78f4b5375eb4f6e3d9891b83942
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760674"
---
# <a name="set-up-msix-app-attach"></a>Настройка присоединения приложения MSIX

> [!IMPORTANT]
> Присоединение приложения MSIX в настоящее время находится в общедоступной предварительной версии.
> без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом разделе описывается, как настроить подключение приложения MSIX в среде виртуальных рабочих столов Windows.

## <a name="requirements"></a>Требования

Чтобы настроить подключение приложения MSIX, необходимо следующее.

- Доступ к порталу для участников программы предварительной оценки Windows, чтобы загрузить версию Windows 10 с поддержкой API для подключения приложения MSIX.
- Работающее развертывание виртуального рабочего стола Windows. Сведения о развертывании виртуальных рабочих столов Windows (классическая модель) см. в статье [Создание клиента в виртуальном рабочем столе Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Чтобы узнать, как развернуть виртуальный рабочий стол Windows с Azure Resource Managerной интеграцией, см. раздел [Создание пула узлов с портал Azure](./create-host-pools-azure-marketplace.md).
- Средство упаковки MSIX.
- Сетевая папка в развертывании виртуальных рабочих столов Windows, где будет храниться пакет MSIX.

## <a name="get-the-os-image"></a>Получение образа ОС

Сначала необходимо получить образ ОС. Образ ОС можно получить с помощью портал Azure. Однако, если вы являетесь членом программы предварительной оценки Windows, вы можете использовать вместо этого портал предварительной оценки Windows.

### <a name="get-the-os-image-from-the-azure-portal"></a>Получение образа ОС из портал Azure

Чтобы получить образ ОС из портал Azure:

1. Откройте [портал Azure](https://portal.azure.com) и выполните вход.

2. Перейдите к разделу **Создание виртуальной машины**.

3. На вкладке " **базовый** " выберите **многосеансовый выпуск Windows 10 Корпоративная, версия 2004**.

4. Следуйте дальнейшим инструкциям, чтобы завершить создание виртуальной машины.

     >[!NOTE]
     >Эту виртуальную машину можно использовать для непосредственного тестирования присоединения приложения MSIX. Чтобы получить дополнительные сведения, перейдите к [созданию пакета VHD или VHDX для MSIX](#generate-a-vhd-or-vhdx-package-for-msix). В противном случае продолжите чтение этого раздела.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Получение образа ОС на портале программы предварительной оценки Windows

Чтобы получить образ ОС на портале программы предварительной оценки Windows, выполните следующие действия.

1. Откройте [портал для участников программы предварительной оценки Windows](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) и выполните вход.

     >[!NOTE]
     >Для доступа к порталу для участников предварительной оценки Windows вы должны быть членом этой программы. Дополнительные сведения о программе предварительной оценки Windows см. в [документации по программе предварительной оценке Windows](/windows-insider/at-home/).

2. Прокрутите экран вниз до раздела **Выбор выпуска** и выберите вариант **Windows 10 Insider Preview Корпоративная (FAST) — сборка 19041** или более поздняя.

3. Выберите **Подтвердить**, а затем выберите язык, который вы хотите использовать, и нажмите кнопку **Подтвердить** еще раз.

     >[!NOTE]
     >Пока работа этой функции тестировалась только с английским языком. Вы можете выбрать другой язык, однако он может отображаться некорректно.

4. Когда будет создана ссылка для скачивания, скачайте **64-разрядную** версию и сохраните ее на локальный жесткий диск.

## <a name="prepare-the-vhd-image-for-azure"></a>Подготовка образа виртуального жесткого диска для Azure

Далее необходимо создать главный образ VHD. Если вы еще не создали главный образ виртуального жесткого диска, откройте раздел [Подготовка и настройка главного образа виртуального жесткого диска](set-up-customize-master-image.md) и выполните указанные в нем инструкции.

Создав главный образ виртуального жесткого диска, вы сможете отключить автоматические обновления приложений для присоединения приложения MSIX. Чтобы отключить автоматическое обновление, необходимо выполнить в командной строке с повышенными привилегиями следующие команды.

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

После отключения автоматического обновления необходимо включить Hyper-V, так как вы будете использовать команду mount-VHD для промежуточного размещения и отключения виртуального жесткого диска для отмены размещения.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Чтобы это вступило в силу, перезапустите виртуальную машину.

Затем подготовьте виртуальный жесткий диск виртуальной машины для Azure и отправьте полученный виртуальный жесткий диск в Azure. Дополнительные сведения см. в разделе [Подготовка и настройка главного образа виртуального жесткого диска](set-up-customize-master-image.md).

После того как вы отправите виртуальный жесткий диск в Azure, создайте пул узлов на основе этого нового образа, следуя инструкциям из учебника [Создание пула узлов с использованием Azure Marketplace](create-host-pools-azure-marketplace.md).

## <a name="prepare-the-application-for-msix-app-attach"></a>Подготовка приложения для подключения приложения MSIX

Если у вас уже есть пакет MSIX, перейдите к разделу [Настройка инфраструктуры виртуальных рабочих столов](#configure-windows-virtual-desktop-infrastructure). Если вы хотите протестировать устаревшие приложения, преобразуйте устаревшее приложение в пакет MSIX, следуя инструкциям в разделе [Создание пакета MSIX из классической программы установки на виртуальной машине](/windows/msix/packaging-tool/create-app-package-msi-vm/).

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Создание пакета VHD или VHDX для MSIX

Для оптимизации производительности используются пакеты в формате VHD или VHDX. Для правильной работы MSIX требуются пакеты VHD или VHDX.

Чтобы создать пакет VHD или VHDX для MSIX, выполните следующие действия.

1. [Скачайте инструмент msixmgr](https://aka.ms/msixmgr) и сохраните ZIP-архив в папку на виртуальной машине с узлом сеансов.

2. Распакуйте ZIP-архив с инструментом msixmgr.

3. Поместите исходный пакет MSIX в ту же папку, в которую вы распаковали инструмент msixmgr.

4. Выполните в PowerShell следующий командлет, чтобы создать виртуальный жесткий диск:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Убедитесь, что размера виртуального жесткого диска достаточно для размещения развернутого MSIX*.

5. Выполните следующий командлет, чтобы подключить только что созданный виртуальный жесткий диск:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Выполните следующий командлет, чтобы инициализировать виртуальный жесткий диск:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Выполните следующий командлет, чтобы создать раздел:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Выполните следующий командлет, чтобы отформатировать этот раздел:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Создайте на подключенном виртуальном жестком диске родительскую папку. Этот шаг обязателен, поскольку для подключения приложения MSIX требуется родительская папка. Родительской папке можно присвоить любое имя.

### <a name="expand-msix"></a>Развертывание MSIX

После этого необходимо "развернуть" образ MSIX, выполнив его распаковку. Чтобы распаковать образ MSIX, выполните следующие действия.

1. Откройте командную строку от имени администратора и перейдите к папке, в которую вы скачали и распаковали инструмент msixmgr.

2. Выполните следующий командлет, чтобы распаковать MSIX в виртуальный жесткий диск, созданный и подключенный в предыдущем разделе.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    После распаковки появится следующее сообщение:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > При использовании пакетов из Магазина Майкрософт для бизнеса (или образовательных учреждений) в сети или на устройствах, не подключенных к Интернету, для успешного запуска приложения необходимо получить лицензии пакета из Магазина и установить их. См. раздел [Использование пакетов в автономном режиме](#use-packages-offline).

3. Перейдите к подключенному виртуальному жесткому диску, откройте папку приложения и проверьте наличие содержимого пакета.

4. Отключите виртуальный жесткий диск.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Настройка инфраструктуры виртуальных рабочих столов Windows

Один развернутый пакет MSIX (виртуальный жесткий диск, созданный в предыдущем разделе) могут использовать совместно сразу несколько виртуальных машин узла сеансов, так как виртуальные жесткие диски подключаются в режиме только для чтения.

Прежде чем начать, убедитесь, что сетевая папка соответствует следующим требованиям.

- Сетевая папка должна быть совместима с SMB.
- Виртуальные машины, которые входят в пул узлов сеансов, имеют разрешения NTFS для сетевой папки.

### <a name="set-up-an-msix-app-attach-share"></a>Настройка сетевой папки для подключения приложения MSIX

В среде виртуальных рабочих столов Windows создайте сетевую папку и переместите в нее пакет.

>[!NOTE]
> Для создания сетевых папок MSIX рекомендуется настраивать сетевую папку с разрешениями NTFS только для чтения.

## <a name="install-certificates"></a>Установка сертификатов

Если в приложении используется сертификат, который не является общедоступным доверенным или является самозаверяющим, установите его следующим образом:

1. Щелкните пакет правой кнопкой мыши и выберите **Свойства**.
2. В открывшемся окне выберите вкладку **Цифровые подписи**. В списке на этой вкладке должен быть указан только один элемент, как показано ниже. Выдерите этот элемент, а затем выберите **Сведения**.
3. Когда появится окно сведения о цифровой подписи, перейдите на вкладку **Общие** , выберите **Просмотр сертификата**, а затем — **установить сертификат**.
4. Когда откроется установщик, выберите установку на **локальный компьютер** и нажмите **Далее**.
5. Если установщик спросит, нужно ли разрешить приложению вносить изменения на вашем устройстве, выберите **Да**.
6. Выберите **Поместить все сертификаты в следующее хранилище** и нажмите **Обзор**.
7. Когда откроется окно хранилища сертификатов, выберите **Доверенные лица** и нажмите **ОК**.
8. Выберите **Далее** и **Готово**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Подготовка сценариев PowerShell для подключения к приложению MSIX

Подключение приложения MSIX состоит из четырех этапов, которые необходимо выполнить в следующем порядке:

1. Этап
2. Зарегистрировать
3. Отмена регистрации
4. Отмена размещения

На каждом этапе создается сценарий PowerShell. Примеры сценариев для каждого этапа см. [здесь](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Размещение сценария PowerShell

Перед обновлением сценариев PowerShell убедитесь, что вы знаете GUID тома на виртуальном жестком диске. Чтобы узнать GUID тома, выполните следующие действия.

1.  Откройте сетевую папку с виртуальным жестким диском на той виртуальной машине, где вы собираетесь выполнить сценарий.

2.  Щелкните виртуальный жесткий диск правой кнопкой мыши файл и выберите **Подключить**. Виртуальный жесткий диск будет подключен к соответствующему диску.

3.  После подключения виртуального жесткого диска откроется **Проводник**. Запишите родительскую папку и обновите переменную **$parentFolder**.

    >[!NOTE]
    >Если родительская папка не отображается, это значит, что MSIX был развернут неправильно. Повторите действия, описанные в предыдущем разделе, и попробуйте еще раз.

4.  Откройте родительскую папку. При правильном развертывании вы увидите папку с таким же именем, как у пакета. Обновите переменную **$packageName**, присвоив ей имя этой папки.

    Например, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Откройте окно командной строки и введите **mountvol**. Эта команда открывает список томов и их GUID. Скопируйте GUID тома, в котором буква диска соответствует диску, к которому вы подключили виртуальный жесткий диск в шаге 2.

    Например, в этом примере выходных данных команды mountvol при подключении виртуального жесткого диска к диску C необходимо скопировать значение, которое находится над `C:\`.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Обновите переменную **$volumeGuid**, присвоив ей только что скопированный GUID тома.

7. Откройте командную строку PowerShell с правами администратора и обновите следующий сценарий PowerShell, указав переменные, которые относятся к вашей среде.

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

    Mount-VHD -Path $vhdSrc -NoDriveLetter -ReadOnly

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

### <a name="register-powershell-script"></a>Регистрация сценария PowerShell

Чтобы выполнить сценарий регистрации, выполните следующие командлеты PowerShell, заменив значения заполнителей на значения, которые относятся к вашей среде.

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

### <a name="deregister-powershell-script"></a>Отмена регистрации сценария PowerShell

Для этого сценария замените заполнитель переменной **$packageName** на имя проверяемого пакета.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Отмена размещения сценария PowerShell

Для этого сценария замените заполнитель переменной **$packageName** на имя проверяемого пакета.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Настройка сценариев моделирования для агента подключения приложения MSIX

После создания сценариев пользователи могут запустить их вручную или настроить на автоматический запуск в качестве сценариев запуска, входа в систему, выхода из системы и завершения работы. Дополнительные сведения об этих типах сценариев см. в разделе [Использование сценариев запуска, завершения работы, входа и выхода из системы в групповой политике](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Каждый из этих автоматических сценариев запускает один из этапов сценариев подключения приложения:

- Сценарий запуска запускает сценарий размещения.
- Сценарий входа запускает сценарий регистрации.
- Сценарий выхода из системы запускает сценарий отмены регистрации.
- Сценарий завершения работы запускает сценарий отмены размещения.

## <a name="use-packages-offline"></a>Использование пакетов в автономном режиме

При использовании пакетов из [Магазина Майкрософт для бизнеса](https://businessstore.microsoft.com/) или [Магазина Майкрософт для образовательных учреждений](https://educationstore.microsoft.com/) в сети или на устройствах, не подключенных к Интернету, для успешного запуска приложения необходимо получить лицензии пакета из Магазина и установить их. Если устройство подключено к сети и может подключаться к Магазину Майкрософт для бизнеса, необходимые лицензии должны загружаться автоматически, но, если вы работаете в автономном режиме, лицензии нужно будет настроить вручную.

Чтобы установить файлы лицензий, необходимо использовать сценарий PowerShell, который вызывает класс MDM_EnterpriseModernAppManagement_StoreLicenses02_01 в поставщике WMI Bridge.

Чтобы настроить лицензии для автономного использования, выполните следующие действия.

1. Скачайте пакет приложения, лицензии и необходимые платформы из Магазина Майкрософт для бизнеса. Вам нужны как закодированные, так и незакодированные файлы лицензий. Подробные инструкции по скачиванию см. [здесь](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Обновите следующие переменные в сценарии для шага 3:
      1. `$contentID` — это значение ContentID из незакодированного файла лицензии (файл с расширением XML). Вы можете открыть файл лицензии в любом текстовом редакторе по своему усмотрению.
      2. `$licenseBlob` — это вся строка BLOB-объекта лицензии в закодированном файле лицензии (файл с расширением BIN). Вы можете открыть закодированный файл лицензии в любом текстовом редакторе по своему усмотрению.
3. Выполните следующий сценарий в командной строке администратора PowerShell. Лицензии лучше всего устанавливать в конце [сценария размещения](#stage-the-powershell-script), который также нужно выполнять из командной строки администратора PowerShell.

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

Эта функция пока не поддерживается, но вы можете задать вопросы [техническому сообществу по виртуальным рабочим столам Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Вы также можете оставить отзыв о виртуальном рабочем столе Windows в [центре обратной связи по виртуальным рабочим столам Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
