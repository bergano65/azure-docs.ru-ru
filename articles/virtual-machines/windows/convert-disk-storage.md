---
title: Azure преобразование хранилища управляемых дисков "стандартный" от "премиум" или "премиум" для стандарта | Документация Майкрософт
description: Преобразуемый Azure управляемых дисков "стандартный" от "премиум" или "премиум" на Standard с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417148"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Обновление типа хранилища управляемого диска

Существует четыре диска типы Azure управляемые диски: Azure ultra SSD (Предварительная версия), "премиум" SSD, стандартная SSD и стандартных жестких ДИСКОВ. Можно переключаться между три типа дисков общедоступной версии ("премиум" SSD, стандартная SSD и стандартных жестких ДИСКОВ) в соответствии с потребностями производительности. Вы еще не могут переключаться в ultra диске SSD или из него, необходимо развернуть его.

Эта функция не поддерживается для неуправляемых дисков. Но вы можете легко [преобразовать неуправляемый диск в управляемый диск](convert-unmanaged-to-managed-disks.md) возможность переключаться между типы дисков.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия

* Поскольку преобразования требует перезагрузки виртуальной машины (VM), следует запланировать перенос дисков на предварительно установленный период обслуживания.
* Если диск является неуправляемым, сначала [преобразовать его в управляемый диск](convert-unmanaged-to-managed-disks.md) , можно переключаться между вариантов хранения.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Все управляемые диски виртуальной машины между Premium и Standard

В этом примере показано, как преобразовать все диски виртуальной Машины из Standard в хранилище уровня "премиум" или "премиум" в стандартное хранилище. Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между "стандартный" и "премиум"

Для рабочей нагрузки разработки и тестирования может потребоваться сочетание дисков Standard и Premium для сокращения затрат. Вы можете обновить только эти диски, которым требуется более высокую производительность. В этом примере показано, как для преобразования одного диска виртуальной Машины из Standard в хранилище уровня "премиум" или "премиум" в стандартное хранилище. Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также показано, как выбрать размер, который поддерживает хранилище уровня "премиум":

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Преобразование управляемых дисков уровня "стандартный" от уровня "премиум" на портале Azure

Выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите виртуальную Машину из списка **виртуальных машин** на портале.
3. Если виртуальная машина не остановлена, выберите **остановить** в верхней части виртуальной Машины **Обзор** панели, а также ожидания для виртуальной Машины для остановки.
3. В области для виртуальной Машины, выберите **дисков** в меню.
4. Выберите диск, который требуется преобразовать.
5. Выберите **конфигурации** в меню.
6. Изменение **тип учетной записи** из **стандартный HDD** для **SSD уровня "премиум"** .
7. Нажмите кнопку **Сохранить**и закройте область диска.

Преобразование типа диска происходит мгновенно. Вы можете перезапустить виртуальную Машину после преобразования.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Управляемые диски между стандартный HDD и Standard SSD 

В этом примере показано преобразование одного диска виртуальной Машины из стандартных жестких ДИСКОВ Standard твердотельные или стандартный SSD для стандартных жестких ДИСКОВ:

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
