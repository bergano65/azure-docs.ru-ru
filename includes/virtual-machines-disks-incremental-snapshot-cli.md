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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343055"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Вы можете создать дополнительный снимок с Azure CLI, вам понадобится последняя версия Azure CLI. 

В Windows следующая команда либо установит, либо обновит существующую установку до последней версии:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
На Linux установка CLI будет варьироваться в зависимости от версии операционной системы.  Смотрите [установить Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) для вашей конкретной версии Linux.

Чтобы создать дополнительный снимок, используйте `--incremental` [создание снимка аз](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) с параметром.

Следующий пример создает дополнительный снимок, `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`заменяет, ,`<exampleDiskName>`и `<exampleLocation>` с вашими собственными значениями, а затем запустить пример:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Вы можете определить дополнительные снимки с `SourceResourceId` одного `SourceUniqueId` диска с свойствами снимков. `SourceResourceId`— это идентификатор ресурса Azure Manager родительского диска. `SourceUniqueId`это значение, унаследованное от `UniqueId` свойства диска. Если вы удалили диск, а затем создали новый диск с `UniqueId` тем же именем, значение свойства изменяется.

Можно использовать `SourceResourceId` `SourceUniqueId` и создавать список всех снимков, связанных с определенным диском. В следующем примере будут указаны все дополнительные снимки, связанные с определенным диском, но для этого требуется некоторая настройка.

Этот пример использует jq для запроса данных. Чтобы запустить пример, необходимо [установить jq.](https://stedolan.github.io/jq/download/)

`<yourResourceGroupNameHere>` Заменить `<exampleDiskName>` и с значениями, то вы можете использовать следующий пример, чтобы перечислить существующие дополнительные снимки, до тех пор, как вы также установили jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
