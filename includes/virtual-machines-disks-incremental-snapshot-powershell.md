---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299467"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Поддерживаемые регионы
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Для создания дополнительного снимка можно использовать Azure PowerShell. Вам понадобится последняя версия Azure PowerShell, следующая команда будет либо установить его или обновить существующую установку до последней:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Как только это будет установлено, войдите в сеанс PowerShell с `az login`помощью .

Чтобы создать дополнительный снимок с Azure PowerShell, установите конфигурацию с `-Incremental` [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) с параметром, `-Snapshot` а затем передайте его в качестве переменной [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) через параметр.

`<yourDiskNameHere>`Заменить `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` и с значениями, то вы можете использовать следующий сценарий для создания инкрементного снимка:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Вы можете определить дополнительные снимки с `SourceResourceId` одного `SourceUniqueId` диска с свойствами снимков. `SourceResourceId`— это идентификатор ресурса Azure Manager родительского диска. `SourceUniqueId`это значение, унаследованное от `UniqueId` свойства диска. Если вы удалили диск, а затем создали новый диск с `UniqueId` тем же именем, значение свойства изменяется.

Можно использовать `SourceResourceId` `SourceUniqueId` и создавать список всех снимков, связанных с определенным диском. Замените `<yourResourceGroupNameHere>` значение, а затем вы можете использовать следующий пример, чтобы перечислить существующие дополнительные снимки:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Вы также можете использовать шаблоны менеджерресурсов Azure для создания дополнительного снимка. Вы должны убедиться, что apiVersion установлен на **2019-03-01** и что инкрементное свойство также установлен на истину. Следующий фрагмент является примером того, как создать дополнительный снимок с шаблонами resource Manager:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите увидеть пример кода, демонстрирующий дифференциальную возможность дополнительных снимков, с помощью .NET, см. [Copy Azure Managed Disks в другой регион с дифференциальной возможностью дополнительных снимков.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
