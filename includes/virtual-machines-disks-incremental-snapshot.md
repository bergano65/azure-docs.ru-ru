---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224576"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Создание добавочного моментального снимка (Предварительная версия) для управляемых дисков

Добавочные моментальные снимки (Предварительная версия) являются резервными копиями на момент времени для управляемых дисков, которые, будучи приняты, состоят только из всех изменений с момента последнего моментального снимка. При попытке загрузить или использовать добавочный моментальный снимок используется полный виртуальный жесткий диск. Эта новая возможность для моментальных снимков управляемых дисков может потребовать более экономичного использования, так как больше не требуется хранить весь диск в каждом отдельном моментальном снимке, если вы не решили. Как и обычные моментальные снимки, добавочные моментальные снимки можно использовать для создания полностью управляемого диска или, чтобы создать обычный моментальный снимок.

Между инкрементным моментальным снимком и обычным моментальным снимком существует несколько различий. Добавочные моментальные снимки всегда будут использовать хранилище дисков уровня "Стандартный" независимо от типа хранилища диска, тогда как обычные моментальные снимки могут использовать твердотельные накопители уровня "Премиум". Если вы используете обычные моментальные снимки в хранилище класса Premium для масштабирования развертываний виртуальных машин, мы рекомендуем использовать пользовательские образы в хранилище уровня "Стандартный" в [коллекции общих образов](../articles/virtual-machines/linux/shared-image-galleries.md). Это поможет добиться более сложного масштабирования с меньшими затратами. Кроме того, добавочные моментальные снимки потенциально обеспечивают лучшую надежность с [хранилищем, избыточным](../articles/storage/common/storage-redundancy-zrs.md) в виде зоны (ZRS). Если ZRS доступен в выбранном регионе, добавочный моментальный снимок будет использовать ZRS автоматически. Если ZRS недоступен в регионе, то моментальный снимок будет по умолчанию использовать [локально избыточное хранилище](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Это поведение можно переопределить, выбрав его вручную, но не рекомендуется.

Добавочные моментальные снимки также предлагают функцию разностного копирования, которая уникально доступна для управляемых дисков. Они позволяют получать изменения между двумя добавочными моментальными снимками тех же управляемых дисков на уровне блоков. Эту возможность можно использовать для уменьшения объема данных при копировании моментальных снимков между регионами.

Если вы еще не зарегистрировались для использования предварительной версии и хотите начать использовать добавочные моментальные снимки, напишите нам AzureDisks@microsoft.com по адресу, чтобы получить доступ к общедоступной предварительной версии.

## <a name="restrictions"></a>Ограничения

- В настоящее время добавочные моментальные снимки не могут быть созданы после изменения размера диска.
- В настоящее время добавочные моментальные снимки нельзя перемещать между подписками.
- В настоящее время вы можете создавать URI SAS только для пяти моментальных снимков определенного семейства моментальных снимков в любой конкретный момент времени.
- Нельзя создать добавочный моментальный снимок для конкретного диска за пределами подписки этого диска.
- Каждые пять минут можно создать до семи добавочных моментальных снимков на диск.
- Для одного диска можно создать всего 200 добавочных моментальных снимков.

## <a name="powershell"></a>PowerShell

Для создания добавочного моментального снимка можно использовать Azure PowerShell. Вы можете установить последнюю версию PowerShell локально. Вам потребуется последняя версия Azure PowerShell. Следующая команда либо установит ее, либо обновит существующую установку до последней версии:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

После установки войдите в сеанс PowerShell с помощью `az login`.

Замените `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` и`<yourDesiredSnapShotNameHere>` значениями, чтобы создать добавочный моментальный снимок, можно использовать следующий скрипт:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

## <a name="cli"></a>CLI

Можно создать добавочный моментальный снимок с Azure CLI помощью команды [AZ snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Пример команды будет выглядеть следующим образом:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Можно также определить, какие моментальные снимки являются добавочными моментальными снимками в интерфейсе `--query` командной строки с помощью параметра в команде [AZ snapshot показывать](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Этот параметр можно использовать для прямого запроса свойств **значение sourceresourceid** и **саурцеуникуеид** моментальных снимков. Значение sourceresourceid — это Azure Resource Manager идентификатор ресурса родительского диска. **Саурцеуникуеид** — это значение, унаследованное от свойства **UniqueID** диска. Если удалить диск, а затем создать диск с тем же именем, значение свойства **UniqueID** изменится.

Примеры запросов будут выглядеть следующим образом:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Следующие шаги

Если вы еще не зарегистрировались для использования предварительной версии и хотите начать использовать добавочные моментальные снимки, напишите нам AzureDisks@microsoft.com по адресу, чтобы получить доступ к общедоступной предварительной версии.
