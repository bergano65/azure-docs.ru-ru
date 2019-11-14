---
title: Преобразование хранилища управляемых дисков Azure из уровня "Стандартный" в "Премиум" или "Премиум" в "Стандартный"
description: Сведения о том, как преобразовать хранилище управляемых дисков Azure с уровня "Стандартный" на "Премиум" или "Премиум" на "Стандартный" с помощью Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd3c7112a70850d137f77d28e83c60916a96ea3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036623"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Преобразование хранилища управляемых дисков Azure из уровня "Стандартный" в "Премиум" или "Премиум" в "Стандартный"

Существует четыре типа дисков, управляемых с Azure: Azure Ultra SSDs (Предварительная версия), SSD уровня "Премиум", "Стандартный SSD" и "Стандартный HDD". Вы можете переключаться между тремя типами общедоступного диска (твердотельный SSD, Стандартный SSD и стандартный HDD) в зависимости от потребностей производительности. Вы пока не можете переключиться с Ultra SSD или на него, необходимо развернуть новый.

Эта функция не поддерживается для неуправляемых дисков. Но вы можете легко [преобразовать неуправляемый диск в управляемый](convert-unmanaged-to-managed-disks.md) , чтобы иметь возможность переключаться между типами дисков.

В этой статье показано, как преобразовать управляемые диски с уровня "Стандартный" на "Премиум" или "Премиум" на "Стандартный" с помощью Azure CLI. Сведения об установке или обновлении средства см. в разделе [install Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Перед началом работы

* Для преобразования диска требуется перезагрузка виртуальной машины, поэтому запланируйте перенос дискового накопителя во время работы предварительно существующего периода обслуживания.
* Для неуправляемых дисков сначала [преобразуйте их в управляемые диски](convert-unmanaged-to-managed-disks.md) , чтобы можно было переключаться между вариантами хранения.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Переключение всех управляемых дисков виртуальной машины с уровня "Премиум" на "Стандартный"

В этом примере показано, как преобразовать все диски виртуальной машины из уровня "Стандартный" в хранилище "Премиум" или "Премиум" в хранилище уровня "Стандартный". Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также переключается на размер, поддерживающий хранилище класса Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между уровнями "Стандартный" и "Премиум"

Для рабочей нагрузки для разработки и тестирования может потребоваться сочетание дисков уровня "Стандартный" и "Премиум" для снижения затрат. Можно выбрать обновление только тех дисков, для которых требуется более высокая производительность. В этом примере показано, как преобразовать один диск виртуальной машины из уровня "Стандартный" в хранилище "Премиум" или "Премиум" в хранилище уровня "Стандартный". Чтобы использовать управляемые диски класса Premium, необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище класса Premium. В этом примере также переключается на размер, поддерживающий хранилище класса Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Переключение управляемых дисков между HDD (цен. категория "Стандартный") и SSD (цен. категория "Стандартный")

В этом примере показано, как преобразовать один диск виртуальной машины из HDD (цен. категория "Стандартный") в SSD (цен. категория "Стандартный") или из SSD (цен. категория "Стандартный") в HDD (цен. категория "Стандартный").

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Переключение управляемых дисков между уровнями "Стандартный" и "Премиум" в портал Azure

Выполните следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите виртуальную машину из списка **виртуальных машин**.
3. Если виртуальная машина не остановлена, выберите **остановить** в верхней части панели **Обзор** виртуальной машины и дождитесь остановки виртуальной машины.
4. В области виртуальной машины в меню выберите **диски** .
5. Выберите диск, который требуется преобразовать.
6. В меню выберите пункт **Конфигурация** .
7. Измените **тип учетной записи** с **HDD (цен. Категория "Стандартный")** на **SSD (цен. категория "Премиум")** или с **SSD (цен. Категория "Премиум")** на **HDD (цен. Категория "Стандартный")** .
8. Нажмите кнопку **сохранить**и закройте область диск.

Обновление типа диска происходит мгновенно. После преобразования виртуальную машину можно перезапустить.

## <a name="next-steps"></a>Дополнительная информация

Сделайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).
