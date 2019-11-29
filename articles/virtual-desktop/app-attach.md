---
title: Подключение приложения MSIX виртуальных рабочих столов Windows — Azure
description: Как настроить присоединение приложения MSIX для виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: helohr
ms.openlocfilehash: 54f2c39260ecaf643b0f2add8758ceb866e71afe
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561121"
---
# <a name="set-up-msix-app-attach"></a>Настройка присоединения приложения MSIX

> [!IMPORTANT]
> Присоединение приложения MSIX в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом разделе описано, как настроить подключение приложения MSIX в среде виртуальных рабочих столов Windows.

## <a name="requirements"></a>Требования

Прежде чем приступить к работе, необходимо настроить присоединение приложения MSIX:

- Доступ к порталу программы предварительной оценки Windows для получения версии Windows 10 с поддержкой API-интерфейсов подключения приложения MSIX.
- Работающее развертывание виртуальных рабочих столов Windows. Дополнительные сведения см. [в статье Создание клиента в виртуальном рабочем столе Windows](tenant-setup-azure-active-directory.md).
- Средство упаковки MSIX
- Сетевая папка в развертывании виртуальных рабочих столов Windows, где будет храниться пакет MSIX

## <a name="get-the-os-image"></a>Получение образа ОС

Сначала необходимо получить образ ОС, который будет использоваться для приложения MSIX. Чтобы получить образ ОС, сделайте следующее:

1. Откройте [портал предварительной оценки Windows](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) и выполните вход.

     >[!NOTE]
     >Для доступа к порталу предварительной оценки Windows необходимо быть членом программы предварительной оценки Windows. Дополнительные сведения о программе предварительной оценки Windows см. в [документации по предварительной проверке Windows](https://docs.microsoft.com/windows-insider/at-home/).

2. Прокрутите вниз до раздела **Выбор выпуска** и выберите **Windows 10 Предварительная версия Microsoft Enterprise (быстрая) — сборка XXXXX**.

3. Выберите **подтвердить**, затем выберите нужный язык и нажмите кнопку **подтвердить** еще раз.
    
     >[!NOTE]
     >В настоящее время английский язык является единственным языком, который был протестирован с помощью этой функции. Можно выбрать другие языки, но они могут не отображаться должным образом.
    
4. Когда будет создана ссылка для скачивания, выберите **64-разрядную загрузку** и сохраните ее на локальный жесткий диск.

## <a name="prepare-the-vhd-image-for-azure"></a>Подготовка образа VHD для Azure 

Прежде чем приступить к работе, необходимо создать главный образ VHD. Если вы еще не создали главный VHD-образ, перейдите к статье [Подготовка и настройка главного образа VHD](set-up-customize-master-image.md) и следуйте инструкциям. 

После создания главного образа VHD необходимо отключить автоматическое обновление для приложений, присоединив приложения MSIX. Чтобы отключить автоматическое обновление, необходимо выполнить следующие команды в командной строке с повышенными привилегиями:

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

Затем подготовьте виртуальный жесткий диск виртуальной машины для Azure и отправьте полученный VHD-файл в Azure. Дополнительные сведения см. в статье [Подготовка и настройка главного образа VHD](set-up-customize-master-image.md).

После отправки виртуального жесткого диска в Azure создайте пул узлов, основанный на этом новом образе, следуя инструкциям в руководстве по [созданию пула узлов с помощью Azure Marketplace](create-host-pools-azure-marketplace.md) .

## <a name="prepare-the-application-for-msix-app-attach"></a>Подготовка приложения к подключению приложения MSIX 

Если у вас уже есть пакет MSIX, перейдите к [настройке инфраструктуры виртуальных рабочих столов Windows](#configure-windows-virtual-desktop-infrastructure). Если вы хотите протестировать устаревшие приложения, следуйте инструкциям в статье [Создание пакета MSIX из установщика настольных систем на виртуальной машине](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm) для преобразования устаревшего приложения в пакет MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Создание пакета VHD или VHDX для MSIX

Пакеты имеют формат VHD или VHDX для оптимизации производительности. Для правильной работы MSIX требуются пакеты VHD или VHDX.

Чтобы создать пакет VHD или VHDX для MSIX, выполните следующие действия.

1. [Скачайте средство мсиксмгр](https://aka.ms/msixmgr) и сохраните ZIP-папку в папке на виртуальной машине узла сеансов.

2. Распакуйте папку мсиксмгр Tool. zip.

3. Вставьте исходный пакет MSIX в ту же папку, в которой вы распаковать средство мсиксмгр.

4. Выполните следующий командлет в PowerShell, чтобы создать виртуальный жесткий диск:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Убедитесь, что размер виртуального жесткого диска достаточно велик для размещения развернутого MSIX. *

5. Выполните следующий командлет, чтобы подключить только что созданный виртуальный жесткий диск:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Запустите этот командлет для инициализации виртуального жесткого диска:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Используйте этот командлет для создания новой секции:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Выполните этот командлет, чтобы отформатировать раздел:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Создайте родительскую папку на подключенном виртуальном жестком диске. Этот шаг является обязательным, так как для подключения к приложению MSIX требуется родительская папка. Вы можете присвоить имя родительской папке по своему усмотрению.

### <a name="expand-msix"></a>Развернуть MSIX

После этого необходимо развернуть образ MSIX, выполнив его распаковку. Распаковка образа MSIX:

1. Откройте командную строку от имени администратора и перейдите к папке, в которую был скачан и распакован инструмент мсиксмгр.

2. Выполните следующий командлет, чтобы распаковать MSIX в виртуальный жесткий диск, созданный и подключенный в предыдущем разделе.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    После распаковки должно появиться следующее сообщение:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > При использовании пакетов из Microsoft Store для бизнеса (или образования) в сети или на устройствах, которые не подключены к Интернету, необходимо получить лицензии пакета из магазина и установить их для успешного запуска приложения. См. раздел [Использование пакетов в автономном режиме](#use-packages-offline).

3. Перейдите к подключенному виртуальному жесткому диску и откройте папку приложения и проверьте наличие содержимого пакета.

4. Отключите виртуальный жесткий диск.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Настройка инфраструктуры виртуальных рабочих столов Windows

Один развернутый пакет MSIX (VHD, созданный в предыдущем разделе) может совместно использоваться несколькими виртуальными машинами узла сеансов, так как виртуальные жесткие диски подключены в режиме только для чтения.

Прежде чем начать, убедитесь, что сетевая папка соответствует следующим требованиям:

- Общий ресурс совместим с SMB.
- Виртуальные машины, которые являются частью пула узлов сеансов, имеют разрешения NTFS для общей папки.

### <a name="set-up-an-msix-app-attach-share"></a>Настройка общего ресурса для присоединения приложения MSIX 

В среде виртуальных рабочих столов Windows создайте общую сетевую папку и переместите в нее пакет.

>[!NOTE]
> Для создания общих сетевых ресурсов MSIX рекомендуется настроить общую сетевую папку с разрешениями NTFS только для чтения.

## <a name="install-certificates"></a>Установка сертификатов

Если приложение использует сертификат, который не является доверенным или является самозаверяющим, вот как его установить:

1. Щелкните правой кнопкой мыши пакет и выберите пункт **Свойства**.
2. В появившемся окне выберите вкладку **цифровые подписи** . В списке на вкладке должен быть только один элемент, как показано на следующем рисунке. Выберите этот элемент, чтобы выделить его, а затем выберите * * de
3. Когда появится окно сведения о цифровом сигнале, перейдите на вкладку **Общие** и выберите **установить сертификат**.
4. Когда Установщик откроется, выберите в качестве места хранения **локальный компьютер** , а затем нажмите кнопку **Далее**.
5. Если программа установки запросит, хотите ли вы разрешить приложению вносить изменения на устройстве, выберите **Да**.
6. Выберите **поместить все сертификаты в следующее хранилище**, а затем нажмите кнопку **Обзор**.
7. Когда появится окно Выбор хранилища сертификатов, выберите **Доверенные лица**, а затем нажмите кнопку **ОК**.
8. Выберите **Готово**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Подготовка скриптов PowerShell для подключения к приложению MSIX

Присоединение приложения MSIX состоит из четырех отдельных этапов, которые необходимо выполнить в следующем порядке:

1. Stage
2. Зарегистрироваться
3. Отмена регистрации
4. Отмена размещения

На каждом этапе создается сценарий PowerShell. Примеры сценариев для каждого этапа доступны [здесь](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Разместить сценарий PowerShell

Перед обновлением скриптов PowerShell убедитесь, что у вас есть идентификатор GUID тома в виртуальном жестком диске. Чтобы получить GUID тома, сделайте следующее:

1.  Откройте сетевую папку, в которой виртуальный жесткий диск находится внутри виртуальной машины, где будет выполняться скрипт.

2.  Щелкните правой кнопкой мыши виртуальный жесткий диск и выберите **подключить**. При этом виртуальный жесткий диск будет подключен к букве диска.

3.  После подключения виртуального жесткого диска откроется окно **проводника** . Запись родительской папки и обновление переменной **\$парентфолдер**

    >[!NOTE]
    >Если родительская папка не отображается, это означает, что MSIX не был правильно раскрыт. Повторно выполните предыдущий раздел и повторите попытку.

4.  Откройте родительскую папку. При правильном развертывании вы увидите папку с тем же именем, что и у пакета. Обновите переменную **\$packageName** в соответствии с именем этой папки.

    Пример: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Откройте командную строку и введите команду **mountvol**. Эта команда отобразит список томов и их идентификаторы GUID. Скопируйте идентификатор GUID тома, где буква диска совпадает с диском, к которому подключен виртуальный жесткий диск, на шаге 2.

    Например, в этом примере выходных данных для команды mountvol при подключении виртуального жесткого диска к диску C необходимо скопировать значение, приведенное выше `C:\`.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Обновите переменную **volumeGuid\$** с только что скопированным GUID тома.

7. Откройте командную строку PowerShell с правами администратора и обновите следующий сценарий PowerShell с переменными, которые применяются к вашей среде.

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

    Mount-Diskimage -ImagePath \$vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + \$vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + \$vhdSrc + " has failed!") -BackgroundColor Red

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
    $_.ToString() -eq 'System.Threading.Tasks.Task\`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress\`2[TResult,TProgress])'})[0]

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

### <a name="register-powershell-script"></a>Регистрация скрипта PowerShell

Чтобы выполнить скрипт регистрации, выполните следующие командлеты PowerShell с заданными значениями заполнителями, которые относятся к вашей среде.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path \$path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Отменить регистрацию скрипта PowerShell

Для этого скрипта замените заполнитель для **\$packageName** именем тестируемого пакета.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Отмена размещения скрипта PowerShell

Для этого скрипта замените заполнитель для **\$packageName** именем тестируемого пакета.

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

После создания сценариев пользователи могут вручную запустить их или настроить для автоматического запуска в качестве сценариев запуска, входа в систему, выхода из системы и завершения работы. Дополнительные сведения об этих типах сценариев см. в разделе [Использование сценариев запуска, завершения работы, входа и выхода из системы в групповая политика](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)).

Каждый из этих автоматических сценариев запускается на одном этапе сценария присоединения приложения:

- Сценарий запуска запускает скрипт этапа.
- Сценарий входа запускает сценарий Register.
- Сценарий выхода из системы запускает сценарий дерегистрации.
- Сценарий завершения работы запускает скрипт отмены размещения.

## <a name="use-packages-offline"></a>Использование пакетов в автономном режиме

Если вы используете пакеты из [Microsoft Store для бизнеса](https://businessstore.microsoft.com/) или [Microsoft Store для образовательных учреждений](https://educationstore.microsoft.com/) в сети или на устройствах, которые не подключены к Интернету, необходимо получить лицензии пакета из Microsoft Store и установить их на устройстве, чтобы успешно запустить приложение. Если устройство подключено к сети и может подключаться к Microsoft Store для бизнеса, необходимые лицензии должны загружаться автоматически, но если вы работаете в автономном режиме, вам потребуется настроить лицензии вручную. 

Чтобы установить файлы лицензий, необходимо использовать скрипт PowerShell, который вызывает класс MDM_EnterpriseModernAppManagement_StoreLicenses02_01 в поставщике WMI Bridge.  

Вот как можно настроить лицензии для автономного использования: 

1. Скачайте пакет приложения, лицензии и необходимые платформы из Microsoft Store для бизнеса. Необходимы как закодированные, так и незакодированные файлы лицензий. Подробные инструкции по скачиванию можно найти [здесь](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Обновите следующие переменные в скрипте для шага 3.
      1. `$contentID` — это значение ContentID из файла незакодированной лицензии (. XML). Файл лицензии можно открыть в текстовом редакторе по своему усмотрению.
      2. `$licenseBlob` — это вся строка для большого двоичного объекта лицензии в закодированном файле лицензии (. bin). Файл закодированной лицензии можно открыть в текстовом редакторе по своему усмотрению. 
3. Запустите следующий скрипт из командной строки администратора PowerShell. Место для установки лицензий можно установить в конце [сценария промежуточного хранения](#stage-the-powershell-script) , который также необходимо запустить из командной строки администратора.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

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

В настоящее время эта функция не поддерживается, но вы можете задать вопросы в сообществе на странице [виртуальных рабочих столов Windows течкоммунити](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Вы также можете оставить отзыв о виртуальном рабочем столе Windows в [центре обратной связи виртуальных рабочих столов Windows](https://aka.ms/MRSFeedbackHub)или оставить отзыв о приложении MSIX и средстве упаковки в центре [обратной связи](https://aka.ms/msixappattachfeedback) и в [центре обратной](https://aka.ms/msixtoolfeedback)связи в приложении MSIX.
