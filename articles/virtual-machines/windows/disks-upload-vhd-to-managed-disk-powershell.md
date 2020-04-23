---
title: Загрузите VHD в Azure или скопируйте диск в разных регионах - Azure PowerShell
description: Узнайте, как загрузить VHD на управляемый диск Azure и скопировать управляемый диск в разных регионах с помощью Azure PowerShell с помощью прямой загрузки.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 7c66507989357569828d4ef933cfdca735f71570
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085417"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Загрузите VHD в Azure или скопируйте управляемый диск в другой регион - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Предварительные требования

- Скачать последнюю [версию AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Установка модуля Azure PowerShell.](/powershell/azure/install-Az-ps)
- Если вы собираетесь загрузить VHD из локальных: VHD фиксированного размера, который [был подготовлен для Azure,](prepare-for-upload-vhd-image.md)хранится локально.
- Или управляемый диск в Azure, если вы собираетесь выполнить действие копирования.

## <a name="getting-started"></a>Начало работы

Если вы предпочитаете загружать диски через графический интерфейс, вы можете сделать это с помощью Azure Storage Explorer. Для получения подробной информации успомещено: [Используйте Azure Storage Explorer для управления дисками, управляемыми Azure](disks-use-storage-explorer-managed-disks.md)

Чтобы загрузить VHD в Azure, необходимо создать пустой управляемый диск, настроенный для этого процесса загрузки. Перед созданием, есть некоторые дополнительные сведения, которые вы должны знать об этих дисках.

Этот вид управляемого диска имеет два уникальных состояния:

- ReadToUpload, что означает, что диск готов к загрузке, но, не [защищенная подпись доступа](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) не была создана.
- ActiveUpload, что означает, что диск готов к загрузке и SAS был создан.

> [!NOTE]
> В то время как в любом из этих состояний, управляемый диск будет выставлен счет по [стандартной цене HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)независимо от фактического типа диска. Например, P10 будет выставлен счет как S10. Это будет верно `revoke-access` до тех пор, пока не будет вызван управляемый диск, который необходим для того, чтобы прикрепить диск к VM.

## <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

Прежде чем вы сможете создать пустой стандартный HDD для загрузки, вам понадобится размер файла VHD, который вы хотите загрузить, в байтах. Пример кода получит это для вас, но, чтобы `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`сделать это самостоятельно вы можете использовать: . Это значение используется при указании параметра **-UploadSizeInBytes.**

Теперь, на локальной оболочке, создайте пустой стандартный HDD для загрузки, указав настройки **загрузки** в **параметре -CreateOption,** а также **параметр -UploadSizeInBytes** в смдлете [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Затем позвоните [в New-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) чтобы создать диск.

`<yourdiskname>`Заменить `<yourresourcegroupname>`, `<yourregion>` а затем запустить следующие команды:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Если вы хотите загрузить либо премиум SSD или стандартный SSD, замените **Standard_LRS** либо **Premium_LRS,** либо **StandardSSD_LRS.** Ультрадиски еще не поддерживаются.

Теперь, когда вы создали пустой управляемый диск, настроенный для процесса загрузки, вы можете загрузить на него VHD. Чтобы загрузить VHD на диск, вам понадобится пишущее SAS, чтобы вы могли ссылаться на него в качестве места назначения для загрузки.

Для создания вывешивого SAS вашего пустого управляемого диска, замените `<yourdiskname>`и `<yourresourcegroupname>`, затем используйте следующие команды:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Отправка VHD

Теперь, когда у вас есть SAS для пустого управляемого диска, вы можете использовать его для установки управляемого диска в качестве пункта назначения для вашей команды загрузки.

Используйте AzCopy v10 для загрузки локального файла VHD на управляемый диск, указав созданный SAS URI.

Эта загрузка имеет ту же пропускную выливку, что и [эквивалентный стандартный HDD.](disks-types.md#standard-hdd) Например, если у вас есть размер, который приравнивается к S4, вы будете иметь пропускную стоимость до 60 MiB/s. Но, если у вас есть размер, который приравнивается к S70, вы будете иметь пропускную стоимость до 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

После того, как загрузка завершена, и вам больше не нужно писать больше данных на диск, отозвать SAS. Отмена SAS изменит состояние управляемого диска и позволит прикрепить диск к VM.

`<yourdiskname>`Заменить `<yourresourcegroupname>`и, затем запустить следующую команду:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

Прямая загрузка также упрощает процесс копирования управляемого диска. Вы можете скопировать в пределах той же области или скопировать управляемый диск в другой регион.

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

Теперь, когда вы успешно загрузили VHD на управляемый диск, вы можете прикрепить диск к VM и начать использовать его.

Чтобы узнать, как прикрепить диск данных к VM, смотрите нашу статью по этому вопросу: [Прикрепите диск данных к Windows VM с PowerShell](attach-disk-ps.md). Чтобы использовать диск в качестве диска ОС, [см.](create-vm-specialized.md#create-the-new-vm)
