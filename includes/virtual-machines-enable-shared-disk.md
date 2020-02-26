---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4487beffd0e6a0643f4d3d88f5bb342f23eb848f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202259"
---
## <a name="limitations"></a>Ограничения

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Размеры диска

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Развертывание общего диска Azure

Чтобы развернуть управляемый диск с включенной функцией "общий диск", используйте новое свойство `maxShares` и определите значение `>1`. Это сделает диск общим для нескольких виртуальных машин.

> [!IMPORTANT]
> Значение `maxShares` может быть задано или изменено только при отключении диска от всех виртуальных машин. Допустимые значения для `maxShares`см. в разделе [размеры дисков](#disk-sizes) .

Перед использованием следующего шаблона замените `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`и `[parameters('maxShares')]` собственными значениями.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Использование общих дисков Azure с виртуальными машинами

После развертывания общего диска с `maxShares>1`можно подключить диск к одной или нескольким виртуальным машинам.

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


## <a name="next-steps"></a>Следующие шаги

Если вы заинтересованы в попытке использовать общие диски, [Зарегистрируйтесь для использования предварительной версии](https://aka.ms/shareddisksignup).