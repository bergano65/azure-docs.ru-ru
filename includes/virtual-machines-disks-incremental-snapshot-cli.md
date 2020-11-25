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
ms.openlocfilehash: 4bdeef537556db94338ed50fcfa6e9d88431f25a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016269"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Вы можете создать добавочный моментальный снимок с Azure CLI, вам потребуется последняя версия Azure CLI. 

В Windows Следующая команда установит или обновит существующую установку до последней версии:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
В Linux Установка интерфейса командной строки будет зависеть от версии операционной системы.  См. статью [установка Azure CLI](/cli/azure/install-azure-cli) для конкретной версии Linux.

Чтобы создать добавочный моментальный снимок, используйте команду [AZ snapshot Create](/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) с `--incremental` параметром.

В следующем примере создается добавочный моментальный снимок, заменяются `<yourDesiredSnapShotNameHere>` ,, `<yourResourceGroupNameHere>` `<exampleDiskName>` и `<exampleLocation>` с собственными значениями, а затем выполняется пример:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Можно выявление добавочных моментальных снимков с того же диска со `SourceResourceId` `SourceUniqueId` свойствами и моментальными снимками. `SourceResourceId` Azure Resource Manager идентификатор ресурса родительского диска. `SourceUniqueId` значение, унаследованное от `UniqueId` Свойства диска. Если вы удалили диск, а затем создаете новый диск с тем же именем, значение `UniqueId` Свойства изменится.

`SourceResourceId` `SourceUniqueId` Для создания списка всех моментальных снимков, связанных с определенным диском, можно использовать и. В следующем примере будут перечислены все добавочные моментальные снимки, связанные с конкретным диском, но они требуют установки.

В этом примере для запроса данных используется JQ. Чтобы запустить пример, необходимо [установить JQ](https://stedolan.github.io/jq/download/).

Замените `<yourResourceGroupNameHere>` и `<exampleDiskName>` значениями, затем можно использовать следующий пример для создания списка существующих добавочных моментальных снимков, если вы также установили JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Можно также использовать шаблоны Azure Resource Manager для создания добавочного моментального снимка. Необходимо убедиться, что для apiVersion задано значение **2019-03-01** и для свойства инкремента задано значение true. В следующем фрагменте кода приведен пример создания добавочного моментального снимка с помощью шаблонов диспетчер ресурсов.

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

## <a name="next-steps"></a>Следующие шаги

Если вы хотите просмотреть пример кода, демонстрирующий возможность создания добавочных моментальных снимков с помощью .NET, см. статью [копирование резервных копий управляемых дисков Azure в другой регион с помощью разностных моментальных снимков](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).