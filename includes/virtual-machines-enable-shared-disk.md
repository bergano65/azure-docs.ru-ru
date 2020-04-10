---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008364"
---
## <a name="limitations"></a>Ограничения

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Размеры диска

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Развертывание общих дисков

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Развертывание премиального SSD в качестве общего диска

Для развертывания управляемого диска с включенным функцией общего диска используйте новое свойство `maxShares` и определите значение, превышающее 1. Это делает диск пригодным для общего обмена на нескольких ВМ.

> [!IMPORTANT]
> Значение `maxShares` может быть установлено или изменено только в том случае, если диск не установлен со всех VM. Просмотрите [размеры диска](#disk-sizes) для `maxShares`допустимых значений для.

Перед использованием следующего `[parameters('dataDiskName')]` `[resourceGroup().location]`шаблона, заменить , , `[parameters('dataDiskSizeGB')]`и `[parameters('maxShares')]` с вашими собственными значениями.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Развертывание ультра-диска в качестве общего диска

#### <a name="cli"></a>CLI

Чтобы развернуть управляемый диск с включенным `maxShares` общедисковым диском, измените параметр на значение, превышающее 1. Это делает диск пригодным для общего обмена на нескольких ВМ.

> [!IMPORTANT]
> Значение `maxShares` может быть установлено или изменено только в том случае, если диск не установлен со всех VM. Просмотрите [размеры диска](#disk-sizes) для `maxShares`допустимых значений для.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Для развертывания управляемого диска с включенным `maxShares` функцией общего диска используйте свойство и определите значение больше 1. Это делает диск пригодным для общего обмена на нескольких ВМ.

> [!IMPORTANT]
> Значение `maxShares` может быть установлено или изменено только в том случае, если диск не установлен со всех VM. Просмотрите [размеры диска](#disk-sizes) для `maxShares`допустимых значений для.

Перед использованием следующего `[parameters('dataDiskName')]` `[resourceGroup().location]`шаблона, заменить `[parameters('diskMBpsReadOnly')]` , , `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]`и с вашими собственными значениями.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Использование общих дисков Azure с вашими vMs

После развертывания общего диска `maxShares>1`можно смонтировать диск на один или несколько ваших ВМ.

> [!IMPORTANT]
> Все вмхоза, совместно наделяющие диск, должны быть развернуты в одной и той же [группе размещения.](../articles/virtual-machines/windows/proximity-placement-groups.md)

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Поддерживаемые pr-команды SCSI

После установки общего диска на вгруппа можно установить кворум и прочитать/написать на диск с помощью SCSI PR. Следующие PR-команды доступны при использовании общих дисков Azure:

Чтобы взаимодействовать с диском, начните со списка постоянных резерваций действий:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

При использовании PR_RESERVE, PR_PREEMPT_RESERVATION или PR_RELEASE_RESERVATION укажите один из следующих устойчивых типов бронирования:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

При использовании PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION или PR_RELEASE-RESERVATION необходимо обеспечить стойкий ключ к бронированию.


## <a name="next-steps"></a>Следующие шаги

Если вы заинтересованы в попытке общих дисков, [подпишитесь на наш предварительный просмотр.](https://aka.ms/AzureSharedDiskPreviewSignUp)