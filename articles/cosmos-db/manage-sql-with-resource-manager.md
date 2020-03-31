---
title: Создание и управление DB Azure Cosmos с помощью шаблонов управления ресурсами
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки Azure Cosmos DB для API
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251847"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Управление aPI-ресурсами API Azure Cosmos DB S'L (Core) с помощью шаблонов управления ресурсами Azure

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для автоматизации управления учетными записями, базами данных и контейнерами Azure Cosmos DB.

В этой статье показаны только примеры шаблонов шаблонов ресурсов Azure Для учетных записей API. Вы также можете найти примеры шаблонов для [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)и [AIS таблицы.](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Создание учетной записи, базы данных и контейнера Azure Cosmos

Следующий шаблон менеджера ресурсов Azure создает учетную запись Azure Cosmos с:

* На уровне базы данных два контейнера с пропускной емкостью 400 запрашиваемых единиц (RU/s).
* Один контейнер с выделенной пропускной емкостью 400 руб./с.

Чтобы создать dB-ресурсы Azure Cosmos, скопируйте следующий шаблон примера и разместите его в описанном виде, либо через [PowerShell,](#deploy-via-powershell) либо [через Azure CLI.](#deploy-via-azure-cli)

* Дополнительно можно посетить [галерею быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) и развернуть шаблон с портала Azure.
* Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

> [!IMPORTANT]
>
> * При добавлении или удалении местоположений в учетную запись Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны проводиться отдельно.
> * Имена учетных записей ограничены 44 символами, все в нижнем регистре.
> * Чтобы изменить значения пропускной способности, повторно отправьте шаблон с обновленными RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Чтобы создать контейнер с большим ключом раздела, измените предыдущий шаблон, чтобы включить `"version":2` свойство в `partitionKey` объект.

### <a name="deploy-via-powershell"></a>Развертывание через PowerShell

Использовать PowerShell для развертывания шаблона управления ресурсами Azure:

1. **Скопируйте** сценарий.
2. Выберите **примерить,** чтобы открыть облачную оболочку Azure.
3. Нажмите в окне облачной оболочки Azure, а затем выберите **Paste.**

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Вы можете развернуть шаблон с локально установленной версией PowerShell вместо Azure Cloud Shell. Вам нужно будет [установить модуль Azure PowerShell.](/powershell/azure/install-az-ps) Выполнить, `Get-Module -ListAvailable Az` чтобы найти требуемую версию.

### <a name="deploy-via-azure-cli"></a>Развертывание через Azure CLI

Использовать Azure CLI для развертывания шаблона управления ресурсами Azure:

1. **Скопируйте** сценарий.
2. Выберите **примерить,** чтобы открыть облачную оболочку Azure.
3. Нажмите в окне облачной оболочки Azure, а затем выберите **Paste.**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Команда `az cosmosdb show` показывает недавно созданную учетную запись Azure Cosmos после ее подготовки. Вы можете развернуть шаблон с локально установленной версией Azure CLI вместо Azure Cloud Shell. Для получения дополнительной информации смотрите статью [Azure Command-Line Interface (CLI).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Создание dB-контейнера Azure Cosmos с функциональностью сервера

Шаблон управления ресурсами Azure можно использовать для создания dB-контейнера Azure Cosmos s s с сохраненной процедурой, триггером и функцией, определяемой пользователем.

Скопируйте следующий пример шаблона и разместите его в описанном виде, либо с [PowerShell,](#deploy-with-powershell) либо [с Помощью Azure CLI.](#deploy-with-azure-cli)

* Дополнительно можно посетить [галерею Azure quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) и развернуть шаблон с портала Azure.
* Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

Использовать PowerShell для развертывания шаблона управления ресурсами Azure:

1. **Скопируйте** сценарий.
1. Выберите **примерить,** чтобы открыть облачную оболочку Azure.
1. Нажмите в правой кнопке «Окно облачной **оболочки»** Azure, а затем выберите Paste .

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Вы можете развернуть шаблон с локально установленной версией PowerShell вместо Azure Cloud Shell. Вам нужно будет [установить модуль Azure PowerShell.](/powershell/azure/install-az-ps) Выполнить, `Get-Module -ListAvailable Az` чтобы найти требуемую версию.

### <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Использовать Azure CLI для развертывания шаблона управления ресурсами Azure:

1. **Скопируйте** сценарий.
2. Выберите **примерить,** чтобы открыть облачную оболочку Azure.
3. Нажмите в окне облачной оболочки Azure, а затем выберите **Paste.**

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны Azure Cosmos DB Квикстарт](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Устранение проблем общие ошибки в развертывании ресурсов Ресурсов Azure](../azure-resource-manager/templates/common-deployment-errors.md)
