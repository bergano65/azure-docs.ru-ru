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
ms.openlocfilehash: ee8a711a867f8abdc831b0d1d9d0b504b1104955
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310123"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Создание добавочного моментального снимка (Предварительная версия) для управляемых дисков

Добавочные моментальные снимки (Предварительная версия) являются резервными копиями на момент времени для управляемых дисков, которые, будучи приняты, состоят только из всех изменений с момента последнего моментального снимка. При попытке загрузить или использовать добавочный моментальный снимок используется полный виртуальный жесткий диск. Эта новая возможность для моментальных снимков управляемых дисков может потребовать более экономичного использования, так как больше не требуется хранить весь диск в каждом отдельном моментальном снимке, если вы не решили. Как и обычные моментальные снимки, добавочные моментальные снимки можно использовать для создания полностью управляемого диска или, чтобы создать обычный моментальный снимок.

Между инкрементным моментальным снимком и обычным моментальным снимком существует несколько различий. Добавочные моментальные снимки всегда будут использовать хранилище дисков уровня "Стандартный" независимо от типа хранилища диска, тогда как обычные моментальные снимки могут использовать твердотельные накопители уровня "Премиум". Если вы используете обычные моментальные снимки в хранилище класса Premium для масштабирования развертываний виртуальных машин, мы рекомендуем использовать пользовательские образы в хранилище уровня "Стандартный" в [коллекции общих образов](../articles/virtual-machines/linux/shared-image-galleries.md). Это поможет добиться более сложного масштабирования с меньшими затратами. Кроме того, добавочные моментальные снимки потенциально обеспечивают лучшую надежность с [хранилищем, избыточным](../articles/storage/common/storage-redundancy-zrs.md) в виде зоны (ZRS). Если ZRS доступен в выбранном регионе, добавочный моментальный снимок будет использовать ZRS автоматически. Если ZRS недоступен в регионе, то моментальный снимок будет по умолчанию использовать [локально избыточное хранилище](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Это поведение можно переопределить, выбрав его вручную, но не рекомендуется.

Добавочные моментальные снимки также предлагают функцию разностного копирования, которая уникально доступна для управляемых дисков. Они позволяют получать изменения между двумя добавочными моментальными снимками тех же управляемых дисков на уровне блоков. Эту возможность можно использовать для уменьшения объема данных при копировании моментальных снимков между регионами.

Если вы еще не зарегистрировались для использования предварительной версии и хотите начать использовать добавочные моментальные снимки, напишите нам AzureDisks@microsoft.com по адресу, чтобы получить доступ к общедоступной предварительной версии.

## <a name="restrictions"></a>Ограничения

- Добавочные моментальные снимки в настоящее время доступны только в западной центральной части США.
- В настоящее время добавочные моментальные снимки не могут быть созданы после изменения размера диска.
- В настоящее время добавочные моментальные снимки нельзя перемещать между подписками.
- В настоящее время вы можете создавать URI SAS только для пяти моментальных снимков определенного семейства моментальных снимков в любой конкретный момент времени.
- Нельзя создать добавочный моментальный снимок для конкретного диска за пределами подписки этого диска.
- Каждые пять минут можно создать до семи добавочных моментальных снимков на диск.
- Для одного диска можно создать всего 200 добавочных моментальных снимков.

## <a name="powershell"></a>PowerShell

Для создания добавочного моментального снимка можно использовать Azure PowerShell. Вам потребуется последняя версия Azure PowerShell. Следующая команда либо установит ее, либо обновит существующую установку до последней версии:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

После установки войдите в сеанс PowerShell с помощью `az login`.

Чтобы создать добавочный моментальный снимок с Azure PowerShell, настройте конфигурацию с помощью командлета [New-азснапшотконфиг](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) с `-Incremental` параметром, а затем передайте эту переменную в `-Snapshot` командлет [New-азснапшот](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) с помощью параметра.

Замените `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` и`<yourDesiredSnapShotNameHere>` значениями, чтобы создать добавочный моментальный снимок, можно использовать следующий скрипт:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Можно выявление добавочных моментальных снимков с того же диска `SourceResourceId` со `SourceUniqueId` свойствами и моментальными снимками. `SourceResourceId`Azure Resource Manager идентификатор ресурса родительского диска. `SourceUniqueId`значение, унаследованное от `UniqueId` свойства диска. Если вы удалили диск, а затем создаете новый диск с тем же именем, значение `UniqueId` свойства изменится.

Для создания списка `SourceResourceId` всех `SourceUniqueId` моментальных снимков, связанных с определенным диском, можно использовать и. Замените `<yourResourceGroupNameHere>` на свое значение, а затем можно использовать следующий пример для создания списка существующих добавочных моментальных снимков:

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

## <a name="cli"></a>CLI

Вы можете создать добавочный моментальный снимок с Azure CLI, вам потребуется последняя версия Azure CLI. Следующая команда установит или обновит существующую установку до последней версии:

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

Чтобы создать добавочный моментальный снимок, используйте команду [AZ snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) с `--incremental` параметром.

В следующем примере создается добавочный моментальный снимок `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`заменяются `<exampleLocation>` `<exampleDiskName>`,, и с собственными значениями, а затем выполняется пример:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Можно выявление добавочных моментальных снимков с того же диска `SourceResourceId` со `SourceUniqueId` свойствами и моментальными снимками. `SourceResourceId`Azure Resource Manager идентификатор ресурса родительского диска. `SourceUniqueId`значение, унаследованное от `UniqueId` свойства диска. Если вы удалили диск, а затем создаете новый диск с тем же именем, значение `UniqueId` свойства изменится.

Для создания списка `SourceResourceId` всех `SourceUniqueId` моментальных снимков, связанных с определенным диском, можно использовать и. В следующем примере будут перечислены все добавочные моментальные снимки, связанные с конкретным диском, но они требуют установки.

В этом примере для запроса данных используется JQ. Чтобы запустить пример, необходимо [установить JQ](https://stedolan.github.io/jq/download/).

Замените `<yourResourceGroupNameHere>` и`<exampleDiskName>` значениями, затем можно использовать следующий пример для создания списка существующих добавочных моментальных снимков, если вы также установили JQ:

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

Если вы еще не зарегистрировались для использования предварительной версии и хотите начать использовать добавочные моментальные снимки, напишите нам AzureDisks@microsoft.com по адресу, чтобы получить доступ к общедоступной предварительной версии.
