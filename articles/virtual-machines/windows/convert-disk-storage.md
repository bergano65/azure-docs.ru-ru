---
title: Преобразование хранилища управляемых дисков Azure из уровня "Стандартный" в "Премиум" или "Премиум" в "Стандартный" | Документация Майкрософт
description: Как преобразовать управляемые диски Azure с уровня "Стандартный" на "Премиум" или "Премиум" на "Стандартный" с помощью Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 274697d555974f286c2c71f26280640077c11f81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749549"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Обновление типа хранилища управляемого диска

Существует четыре типа дисков, управляемых с Azure: Azure Ultra SSDs (Предварительная версия), SSD уровня "Премиум", "Стандартный SSD" и "Стандартный HDD". Вы можете переключаться между тремя типами общедоступного диска (твердотельный SSD, Стандартный SSD и стандартный HDD) в зависимости от потребностей производительности. Вы пока не можете переключиться с Ultra SSD или на него, необходимо развернуть новый.

Эта функция не поддерживается для неуправляемых дисков. Но вы можете легко [преобразовать неуправляемый диск в управляемый](convert-unmanaged-to-managed-disks.md) , чтобы иметь возможность переключаться между типами дисков.

 

## <a name="prerequisites"></a>Предварительные требования

* Так как для преобразования требуется перезагрузка виртуальной машины, необходимо запланировать миграцию дискового накопителя во время работы предварительно существующего периода обслуживания.
* Если диск не управляется, сначала [преобразуйте его в управляемый диск](convert-unmanaged-to-managed-disks.md) , чтобы можно было переключаться между вариантами хранения.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Переключение всех управляемых дисков виртуальной машины с уровня "Премиум" на "Стандартный"

В этом примере показано, как преобразовать все диски виртуальной машины из уровня "Стандартный" в хранилище "Премиум" или "Премиум" в хранилище уровня "Стандартный". Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между уровнями "Стандартный" и "Премиум"

Для рабочей нагрузки разработки и тестирования может потребоваться сочетание дисков уровня "Стандартный" и "Премиум" для снижения затрат. Можно выбрать обновление только тех дисков, для которых требуется более высокая производительность. В этом примере показано, как преобразовать один диск виртуальной машины из уровня "Стандартный" в хранилище "Премиум" или "Премиум" в хранилище уровня "Стандартный". Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также показано, как переключиться на размер, поддерживающий хранилище класса Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Преобразование управляемых дисков из уровня "Стандартный" в "Премиум" в портал Azure

Выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите виртуальную машину из списка **виртуальных машин** на портале.
3. Если виртуальная машина не остановлена, выберите **остановить** в верхней части панели **Обзор** виртуальных машин и дождитесь остановки виртуальной машины.
3. В области виртуальной машины в меню выберите **диски** .
4. Выберите диск, который требуется преобразовать.
5. В меню выберите пункт **Конфигурация** .
6. Измените **тип учетной записи** с **HDD (цен. Категория "Стандартный")** на **SSD (цен. Категория "Премиум")** .
7. Нажмите кнопку **сохранить**и закройте область диск.

Преобразование типа диска происходит мгновенно. После преобразования виртуальную машину можно перезапустить.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Переключение управляемых дисков между HDD (цен. категория "Стандартный") и SSD (цен. категория "Стандартный") 

В этом примере показано, как преобразовать один диск виртуальной машины из HDD (цен. категория "Стандартный") в SSD (цен. категория "Стандартный") или из SSD (цен. категория "Стандартный") в HDD (цен. категория "Стандартный"):

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).
