---
title: Загрузите vhd в Azure с помощью Azure PowerShell
description: Узнайте, как загрузить vhd на управляемый диск Azure и скопировать управляемый диск в разных регионах с помощью Azure PowerShell с помощью прямой загрузки.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369562"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Загрузите vhd в Azure с помощью Azure PowerShell

В этой статье объясняется, как загрузить vhd с локальной машины на управляемый диск Azure. Ранее вы должны были следовать более вовлеченный процесс, который включал постановку данных в учетную запись хранения и управление этой учетной записью хранения. Теперь вам больше не нужно управлять учетной записью хранения или этапом данных в нем, чтобы загрузить vhd. Вместо этого вы создаете пустой управляемый диск и загружаете vhd непосредственно на него. Это упрощает загрузку наемных ВМ в Azure и позволяет загружать vhd до 32 TiB непосредственно в большой управляемый диск.

Если вы предоставляете решение для резервного копирования IaaS VMs в Azure, мы рекомендуем вам использовать прямую загрузку для восстановления резервных копирования клиентов на управляемых дисках. Если вы загружаете VHD из внешней машины в Azure, скорость зависит от локальной пропускной способности. Если вы используете Azure VM, то пропускная способность будет такой же, как стандартные HDD.

В настоящее время прямая загрузка поддерживается для стандартных ДИСКов, стандартных SSD и премиальных дисков SSD. Он еще не поддерживается для ультра SSD.

## <a name="prerequisites"></a>Предварительные требования

- Скачать последнюю [версию AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Установка модуля Azure PowerShell.](/powershell/azure/install-Az-ps)
- Если вы собираетесь загрузить VHD из локальных: VHD фиксированного размера, который [был подготовлен для Azure,](prepare-for-upload-vhd-image.md)хранится локально.
- Или управляемый диск в Azure, если вы собираетесь выполнить действие копирования.

## <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

Чтобы загрузить vhd в Azure, необходимо создать пустой управляемый диск, настроенный для этого процесса загрузки. Перед созданием, есть некоторые дополнительные сведения, которые вы должны знать об этих дисках.

Этот вид управляемого диска имеет два уникальных состояния:

- ReadToUpload, что означает, что диск готов к загрузке, но, не [защищенная подпись доступа](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) не была создана.
- ActiveUpload, что означает, что диск готов к загрузке и SAS был создан.

В то время как в любом из этих состояний, управляемый диск будет выставлен счет по [стандартной цене HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)независимо от фактического типа диска. Например, P10 будет выставлен счет как S10. Это будет верно `revoke-access` до тех пор, пока не будет вызван управляемый диск, который необходим для того, чтобы прикрепить диск к VM.

Перед созданием пустого стандартного HDD для загрузки вам понадобится размер файла в байтах vhd, который вы хотите загрузить. Пример кода получит это для вас, но, чтобы `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`сделать это самостоятельно вы можете использовать: . Это значение используется при указании параметра **-UploadSizeInBytes.**

Теперь, на локальной оболочке, создайте пустой стандартный HDD для загрузки, указав настройки **загрузки** в **параметре -CreateOption,** а также **параметр -UploadSizeInBytes** в смдлете [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Затем позвоните [в New-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) чтобы создать диск:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Если вы хотите загрузить либо премиум SSD или стандартный SSD, замените **Standard_LRS** либо **Premium_LRS,** либо **StandardSSD_LRS.** Ultra SSD пока не поддерживается.

Теперь вы создали пустой управляемый диск, настроенный для процесса загрузки. Чтобы загрузить vhd на диск, вам понадобится пишущее SAS, так что вы можете ссылаться на него в качестве места назначения для загрузки.

Для создания вывешивого SAS вашего пустого управляемого диска используйте следующую команду:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Загрузить vhd

Теперь, когда у вас есть SAS для пустого управляемого диска, вы можете использовать его для установки управляемого диска в качестве пункта назначения для вашей команды загрузки.

Используйте AzCopy v10 для загрузки локального файла VHD на управляемый диск, указав созданный SAS URI.

Эта загрузка имеет ту же пропускную выливку, что и [эквивалентный стандартный HDD.](disks-types.md#standard-hdd) Например, если у вас есть размер, который приравнивается к S4, вы будете иметь пропускную стоимость до 60 MiB/s. Но, если у вас есть размер, который приравнивается к S70, вы будете иметь пропускную стоимость до 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

После того, как загрузка завершена, и вам больше не нужно писать больше данных на диск, отозвать SAS. Отмена SAS изменит состояние управляемого диска и позволит прикрепить диск к VM.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

Прямая загрузка также упрощает процесс копирования управляемого диска. Вы можете скопировать в пределах одного региона или кросс-региона (в другой регион).

Последующий сценарий сделает это за вас, процесс аналогичен описанным ранее шагам, с некоторыми отличиями, так как вы работаете с существующим диском.

> [!IMPORTANT]
> При предоставлении размера диска в байтах управляемого диска из Azure необходимо добавить смещение 512. Это происходит потому, что Azure опускает колонтитул при возврате размера диска. Копия не удастся, если вы этого не сделаете. Следующий сценарий уже делает это за вас.

Замените `<sourceResourceGroupHere>` `<sourceDiskNameHere>`, `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourOSTypeHere>` `<yourTargetLocationHere>` , , и (пример значения местоположения будет uswest2) с значениями, а затем запустить следующий сценарий для того, чтобы скопировать управляемый диск.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно загрузили vhd на управляемый диск, вы можете прикрепить диск к VM и начать использовать его.

Чтобы узнать, как прикрепить диск данных к VM, смотрите нашу статью по этому вопросу: [Прикрепите диск данных к Windows VM с PowerShell](attach-disk-ps.md). Чтобы использовать диск в качестве диска ОС, [см.](create-vm-specialized.md#create-the-new-vm)
