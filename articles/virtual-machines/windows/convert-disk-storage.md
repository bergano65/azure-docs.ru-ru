---
title: Преобразование управляемого хранилища дисков между стандартным и премиальным SSD
description: Как преобразовать управляемые диски Azure из стандартных в Премиум или Премиум в стандарт с помощью Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720951"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Обновление типа хранилища управляемого диска

Существует четыре типа дисков управляемых дисков Azure: Azure ultra SSD (предварительный просмотр), премиальный SSD, стандартный SSD и стандартный HDD. Вы можете переключаться между тремя типами дисков GA (премиум SSD, стандартный SSD и стандартный HDD) в зависимости от ваших потребностей в производительности. Вы еще не в состоянии переключиться с или на ультра SSD, вы должны развернуть новый.

Эта функция не поддерживается для неуправляемых дисков. Но вы можете легко [преобразовать неуправляемый диск на управляемый диск,](convert-unmanaged-to-managed-disks.md) чтобы иметь возможность переключаться между типами дисков.

 

## <a name="prerequisites"></a>Предварительные требования

* Поскольку для преобразования требуется перезагрузка виртуальной машины (VM), следует запланировать миграцию хранилища диска во время уже существующего окна обслуживания.
* Если диск неуправляемый, сначала [преобразуйте его в управляемый диск,](convert-unmanaged-to-managed-disks.md) чтобы можно было переключаться между вариантами хранения.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Переключите все управляемые диски VM между Premium и Standard

В этом примере показано, как преобразовать все диски VM из стандартного в премиум-хранилище или из Premium в стандартное хранилище. Для использования дисков Premium, управляемых Premium, ваш VM должен использовать [размер VM,](sizes.md) который поддерживает премиум-хранилище. В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между Стандартным и Премиум

Для рабочей нагрузки dev/test может потребоваться сочетание стандартных и премиум-дисков для снижения затрат. Вы можете обновить только те диски, которые нуждаются в лучшей производительности. В этом примере показано, как преобразовать один VM-диск из стандартного в премиум-хранилище или из Premium в хранилище Standard. Для использования дисков Premium, управляемых Premium, ваш VM должен использовать [размер VM,](sizes.md) который поддерживает премиум-хранилище. В этом примере также показано, как переключиться на размер, поддерживающий премиум-хранилище:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Преобразование управляемых дисков со стандарта в Премиум на портале Azure

Выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите VM из списка **виртуальных машин** на портале.
3. Если VM не остановлен, выберите **Остановка** в верхней части панели VM **Overview** и подождите, пока VM остановится.
3. В панели для VM выберите **диски** из меню.
4. Выберите диск, который вы хотите преобразовать.
5. Выберите **конфигурацию** из меню.
6. Измените **тип учетной записи** со **стандартного HDD** на **Premium SSD.**
7. Нажмите **Сохранить**, и закрыть дисковое стекло.

Преобразование типа диска мгновенно. Вы можете начать vM после преобразования.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Переключение управляемых дисков между Стандартным HDD и Стандартным SSD 

В этом примере показано, как преобразовать один VM-диск из стандартного HDD в Standard SSD или из Стандартного SSD в Standard HDD:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).
