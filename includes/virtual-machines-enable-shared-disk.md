---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008364"
---
## <a name="limitations"></a>Ограничения

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Размеры диска

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Развертывание общих дисков

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Развертывание SSD уровня "Премиум" как общего диска

Чтобы развернуть управляемый диск с включенной функцией "общий диск", используйте новое `maxShares` свойство и определите значение больше 1. Это сделает диск общим для нескольких виртуальных машин.

> [!IMPORTANT]
> Значение параметра `maxShares` может быть задано или изменено только при отключении диска от всех виртуальных машин. Допустимые значения для `maxShares`см. в разделе [размеры дисков](#disk-sizes) .

Перед использованием следующего шаблона `[parameters('dataDiskName')]`замените, `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]`, и `[parameters('maxShares')]` собственными значениями.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Развертывание Ultra Disk в качестве общего диска

#### <a name="cli"></a>CLI

Чтобы развернуть управляемый диск с включенным компонентом "общий диск" `maxShares` , измените параметр на значение больше 1. Это сделает диск общим для нескольких виртуальных машин.

> [!IMPORTANT]
> Значение параметра `maxShares` может быть задано или изменено только при отключении диска от всех виртуальных машин. Допустимые значения для `maxShares`см. в разделе [размеры дисков](#disk-sizes) .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Чтобы развернуть управляемый диск с включенной функцией "общий диск", используйте `maxShares` свойство и определите значение больше 1. Это сделает диск общим для нескольких виртуальных машин.

> [!IMPORTANT]
> Значение параметра `maxShares` может быть задано или изменено только при отключении диска от всех виртуальных машин. Допустимые значения для `maxShares`см. в разделе [размеры дисков](#disk-sizes) .

Перед использованием следующего шаблона замените `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]`и `[parameters('diskMBpsReadOnly')]` собственными значениями.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Использование общих дисков Azure с виртуальными машинами

После развертывания общего диска с помощью `maxShares>1`его можно подключить к одной или нескольким виртуальным машинам.

> [!IMPORTANT]
> Все виртуальные машины, совместно использующие диск, должны быть развернуты в одной [группе размещения](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

## <a name="supported-scsi-pr-commands"></a>Поддерживаемые команды для запросов на SCSI

После подключения общего диска к виртуальным машинам в кластере можно установить кворум и выполнить чтение и запись на диск с помощью SCSI-запроса. При использовании общих дисков Azure доступны следующие команды.

Чтобы взаимодействовать с диском, начните со списка "постоянное — резервирование — действие":

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

При использовании PR_RESERVE, PR_PREEMPT_RESERVATION или PR_RELEASE_RESERVATION укажите один из следующих типов постоянного резервирования:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Кроме того, при использовании PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION или PR_RELEASE-RESERVATION необходимо указать постоянный ключ-резервирование.


## <a name="next-steps"></a>Дальнейшие шаги

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для использования предварительной версии](https://aka.ms/AzureSharedDiskPreviewSignUp).