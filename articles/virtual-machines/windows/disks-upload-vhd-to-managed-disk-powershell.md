---
title: Отправка виртуального жесткого диска в Azure с помощью Azure PowerShell
description: Узнайте, как передать VHD на управляемый диск Azure и скопировать управляемый диск между регионами с помощью Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: d193dcd0c0539c2daa7220d915fdc3e02c8ea798
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512441"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Отправка виртуального жесткого диска в Azure с помощью Azure PowerShell

В этой статье объясняется, как передать VHD с локального компьютера на управляемый диск Azure. Ранее было необходимо было выполнить более сложный процесс, который включал в себя промежуточное хранение данных в учетной записи хранения и управление этой учетной записью хранения. Теперь вам больше не нужно управлять учетной записью хранения или промежуточными данными в ней для отправки виртуального жесткого диска. Вместо этого создайте пустой управляемый диск и отправьте в него виртуальный жесткий диск. Это упрощает передачу локальных виртуальных машин в Azure и позволяет передавать VHD вплоть до 32 Тиб непосредственно на большой управляемый диск.

Если вы предоставляете решение для резервного копирования виртуальных машин IaaS в Azure, мы рекомендуем использовать прямую передачу для восстановления резервных копий клиентов на управляемые диски. Если вы отправляете виртуальный жесткий диск с компьютера, который является внешним по отношению к Azure, скорости с зависят от локальной пропускной способности. Если вы используете виртуальную машину Azure, пропускная способность будет такой же, как и для стандартных дисков.

В настоящее время прямая отправка поддерживается для дисков уровня "Стандартный", "Стандартный SSD" и "Премиум", управляемых на SSD. Она пока не поддерживается для Ultra SSDs.

## <a name="prerequisites"></a>Технические условия

- Скачайте последнюю [версию AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Установите модуль Azure PowerShell](/powershell/azure/install-Az-ps).
- Если вы собираетесь передать виртуальный жесткий диск из in-PEM: VHD, [подготовленный для Azure](prepare-for-upload-vhd-image.md), который хранится локально.
- Или управляемый диск в Azure, если планируется выполнить действие копирования.

## <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

Чтобы отправить VHD в Azure, необходимо создать пустой управляемый диск, настроенный для этого процесса отправки. Прежде чем создать его, необходимо ознакомиться с дополнительными сведениями об этих дисках.

Этот тип управляемого диска имеет два уникальных состояния:

- Реадтауплоад. Это означает, что диск готов к получению отправки, но [подпись безопасного доступа](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) не была создана.
- Активеуплоад. Это означает, что диск готов к получению отправки, и создан SAS.

Хотя в одном из этих состояний плата за управляемый диск будет взиматься по [стандартному тарифу на HDD](https://azure.microsoft.com/pricing/details/managed-disks/), независимо от фактического типа диска. Например, P10 будет оплачиваться как S10. Это справедливо до тех пор, пока на управляемом диске не будет вызван `revoke-access`, что необходимо для подключения диска к виртуальной машине.

Перед созданием пустого стандартного жесткого диска для отправки вам потребуется размер файла в байтах виртуального жесткого диска, который требуется передать. Пример кода получит это, но для самостоятельного использования можно использовать: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Это значение используется при указании параметра **-уплоадсизеинбитес** .

Теперь в локальной оболочке создайте пустой жесткий диск "Стандартный" для отправки, указав параметр **отправки** в параметре **-CreateOption** , а также параметр **-уплоадсизеинбитес** в командлете [New-аздискконфиг](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Затем вызовите [New-аздиск](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) , чтобы создать диск:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Если вы хотите передать либо твердотельный SSD класса Premium, либо стандартный SSD, замените **Standard_LRS** на **Premium_LRS** или **StandardSSD_LRS**. SSD (цен. категория "Ультра") еще не поддерживается.

Теперь вы создали пустой управляемый диск, настроенный для процесса отправки. Чтобы отправить VHD на диск, вам потребуется доступный для записи SAS, чтобы можно было ссылаться на него как на место назначения для отправки.

Чтобы создать SAS с возможностью записи для пустого управляемого диска, используйте следующую команду:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Отправить виртуальный жесткий диск

Теперь, когда у вас есть SAS для пустого управляемого диска, его можно использовать для задания управляемого диска в качестве места назначения для команды отправки.

Используйте AzCopy V10 для передачи локального VHD-файла на управляемый диск, указав созданный URI SAS.

Эта передача имеет ту же пропускную способность, что и эквивалент [стандартного жесткого диска](disks-types.md#standard-hdd). Например, если размер равен S4, у вас будет пропускная способность до 60 MiB/с. Но если у вас есть размер, который соответствует S70, вы получите пропускную способность до 500 MiB/с.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Если срок действия SAS истекает во время отправки и вы еще не вызывали `revoke-access`, можно получить новый SAS, чтобы продолжить передачу с помощью `grant-access`.

После завершения передачи вам больше не нужно писать какие-либо данные на диск, отозвать SAS. Отзыв SAS изменит состояние управляемого диска и позволит подключить диск к виртуальной машине.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

Прямая загрузка также упрощает процесс копирования управляемого диска. Можно скопировать в один и тот же регион или между регионами (в другой регион).

Следующий сценарий сделает это для вас, процесс аналогичен описанному ранее действиям с некоторыми отличиями, которые возникают после работы с существующим диском.

> [!IMPORTANT]
> При предоставлении размера диска в байтах управляемого диска из Azure необходимо добавить смещение 512. Это обусловлено тем, что при возврате диска в Azure нижний колонтитул опускается. Если этого не сделать, копирование завершится ошибкой. Следующий сценарий уже выполняет это.

Замените `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` и `<yourTargetLocationHere>` (например, значение расположения uswest2) значениями, а затем выполните следующий сценарий, чтобы скопировать управляемый диск.

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

После успешной отправки виртуального жесткого диска на управляемый диск можно подключить его к виртуальной машине и начать использовать.

Сведения о подключении диска данных к виртуальной машине см. в статье о [подключении диска данных к виртуальной машине Windows с помощью PowerShell](attach-disk-ps.md). Сведения о том, как использовать диск в качестве диска операционной системы, см. в статье [Создание виртуальной машины Windows на основе специализированного диска](create-vm-specialized.md#create-the-new-vm).
