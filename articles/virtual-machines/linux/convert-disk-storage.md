---
title: Azure преобразование хранилища управляемых дисков "стандартный" от "премиум" или "премиум" для стандарта | Документация Майкрософт
description: Как преобразовать Azure управляемых дисковых хранилищах "стандартный" от "премиум" или "премиум" на Standard, с помощью Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328788"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure преобразование хранилища управляемых дисков "стандартный" от "премиум" или наоборот

Существует четыре [типы дисков](disks-types.md) для Azure управляемые диски: Azure Ultra дисковый накопитель, "премиум" SSD, стандартная SSD и Standard жесткого диска. Кроме того, можно легко переключаться между SSD класса Premium, Standard SSD и стандартных жестких ДИСКОВ, в соответствии с потребностями производительности с минимальным временем простоя. Эта функция не поддерживается для неуправляемых дисков или Ultra дисковый накопитель. Но вы можете легко [convert неуправляемых на управляемые диски](convert-unmanaged-to-managed-disks.md) возможность переключаться между типы дисков.

В этой статье показано, как преобразование управляемых дисков уровня "стандартный" от "премиум" или "премиум" на Standard с помощью Azure CLI. Чтобы установить или обновить средство, см. в разделе [установить Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Перед началом работы

* Преобразование диска требует перезагрузки виртуальной машины (VM), поэтому запланируйте перенос хранилища дисков на предварительно установленный период обслуживания.
* Для неуправляемых дисков первый [преобразовать для использования управляемых дисков](convert-unmanaged-to-managed-disks.md) , можно переключаться между вариантов хранения.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Все управляемые диски виртуальной машины между Premium и Standard

В этом примере показано, как преобразовать все диски виртуальной Машины из Standard в хранилище уровня "премиум" или "премиум" в стандартное хранилище. Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также выполняется переключение до размера, который поддерживает хранилище класса Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между "стандартный" и "премиум"

Для рабочей нагрузки разработки и тестирования может потребоваться сочетание дисков Standard и Premium для сокращения затрат. Вы можете обновить только эти диски, которым требуется более высокую производительность. В этом примере показано, как для преобразования одного диска виртуальной Машины из Standard в хранилище уровня "премиум" или "премиум" в стандартное хранилище. Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также выполняется переключение до размера, который поддерживает хранилище класса Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Управляемые диски между стандартный HDD и Standard SSD

В этом примере показано, как для преобразования одного диска виртуальной Машины из стандартных жестких ДИСКОВ Standard твердотельные или стандартный SSD для стандартных жестких ДИСКОВ.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Управляемые диски между "стандартный" и "премиум" на портале Azure

Выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите виртуальную Машину из списка **виртуальные машины**.
3. Если виртуальная машина не остановлена, выберите **остановить** в верхней части виртуальной Машины **Обзор** панели, а также ожидания для виртуальной Машины для остановки.
4. В области для виртуальной Машины, выберите **дисков** в меню.
5. Выберите диск, который требуется преобразовать.
6. Выберите **конфигурации** в меню.
7. Изменение **тип учетной записи** из **стандартный HDD** для **SSD уровня "премиум"** или из **SSD уровня "премиум"** для **стандартный HDD**.
8. Выберите **Сохранить**и закройте область диска.

Обновление типа диска происходит мгновенно. Вы можете перезапустить виртуальную Машину после преобразования.

## <a name="next-steps"></a>Дальнейшие действия

Создайте копию виртуальной Машины, доступные только для чтения с помощью [моментальные снимки](snapshot-copy-managed-disk.md).
