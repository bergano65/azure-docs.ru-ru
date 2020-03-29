---
title: Преобразование управляемого хранилища дисков между стандартным и премиальным SSD
description: Как преобразовать хранение управляемых дисков Azure со стандарта на премиум или премиум-стандарт с помощью Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431503"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Преобразование хранилища управляемых дисков Azure со стандарта в Премиум или Премиум в стандартное

Существует четыре типа дисков управляемых дисков Azure: Azure ultra SSD (предварительный просмотр), премиальный SSD, стандартный SSD и стандартный HDD. Вы можете переключаться между тремя типами дисков GA (премиум SSD, стандартный SSD и стандартный HDD) в зависимости от ваших потребностей в производительности. Вы еще не в состоянии переключиться с или на ультра SSD, вы должны развернуть новый.

Эта функция не поддерживается для неуправляемых дисков. Но вы можете легко [преобразовать неуправляемый диск на управляемый диск,](convert-unmanaged-to-managed-disks.md) чтобы иметь возможность переключаться между типами дисков.

В этой статье показано, как преобразовать управляемые диски из стандартных в Премиум или Премиум в стандарт с помощью Azure CLI. Для установки или обновления инструмента [см.](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Перед началом

* Преобразование диска требует перезагрузки виртуальной машины (VM), поэтому запланируйте миграцию хранилища диска во время уже существующего окна обслуживания.
* Для неуправляемых дисков сначала [преобразуйте на управляемые диски,](convert-unmanaged-to-managed-disks.md) чтобы можно было переключаться между вариантами хранения.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Переключите все управляемые диски VM между Premium и Standard

В этом примере показано, как преобразовать все диски VM из стандартного в премиум-хранилище или из Premium в стандартное хранилище. Для использования дисков Premium, управляемых Premium, ваш VM должен использовать [размер VM,](sizes.md) который поддерживает премиум-хранилище. Этот пример также переключается на размер, поддерживающий премиум-хранилище.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Переключение отдельных управляемых дисков между Стандартным и Премиум

Для рабочей нагрузки dev/test вы можете иметь смесь стандартных и премиум дисков, чтобы уменьшить ваши расходы. Вы можете обновить только те диски, которые нуждаются в лучшей производительности. В этом примере показано, как преобразовать один VM-диск из стандартного в премиум-хранилище или из Premium в хранилище Standard. Для использования дисков Premium, управляемых Premium, ваш VM должен использовать [размер VM,](sizes.md) который поддерживает премиум-хранилище. Этот пример также переключается на размер, поддерживающий премиум-хранилище.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Переключение управляемых дисков между Стандартным HDD и Стандартным SSD

В этом примере показано, как преобразовать один VM-диск из стандартного HDD в Standard SSD или со стандартного SSD в Standard HDD.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Переключение управляемых дисков между стандартным и премиум в Портале Azure

Выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите VM из списка **виртуальных машин.**
3. Если VM не остановлен, выберите **«Стоп»** в верхней части панели VM **Overview** и подождите, пока VM остановится.
4. В панели для VM выберите **диски** из меню.
5. Выберите диск, который вы хотите преобразовать.
6. Выберите **конфигурацию** из меню.
7. Измените **тип учетной записи** со **стандартного HDD** на **Premium SSD** или с **Premium SSD** на **Standard HDD.**
8. Выберите **Сохранить**, и закрыть дисковое стекло.

Обновление типа диска мгновенно. Вы можете перезапустить VM после преобразования.

## <a name="next-steps"></a>Дальнейшие действия

Сделайте только для чтения копию VM с помощью [снимков.](snapshot-copy-managed-disk.md)
