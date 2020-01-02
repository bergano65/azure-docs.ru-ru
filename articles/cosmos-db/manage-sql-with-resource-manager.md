---
title: Создание Azure Cosmos DB с помощью шаблонов Azure Resource Manager и управление ими
description: Использование шаблонов Azure Resource Manager для создания и настройки Azure Cosmos DB для API-интерфейса SQL (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 62c04fed03ad2346d0f548a4a8028f2d7d6b3486
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850471"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Управление ресурсами API Azure Cosmos DB SQL (Core) с помощью шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для автоматизации управления учетными записями Azure Cosmos DB, базами данных и контейнерами.

В этой статье показаны Azure Resource Manager примеры шаблонов для учетных записей API SQL. Вы также можете найти примеры шаблонов для API-интерфейсов [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Создание учетной записи, базы данных и контейнера Azure Cosmos

Следующий шаблон Azure Resource Manager создает учетную запись Azure Cosmos с помощью:

* Два контейнера, которые совместно используют 400 запрошенных единиц запросов в секунду на уровне базы данных.
* Один контейнер с выделенной пропускной способностью 400 единиц запросов в секунду.

Чтобы создать Azure Cosmos DB ресурсы, скопируйте приведенный ниже пример шаблона и разверните его, как описано в разделе с помощью [PowerShell](#deploy-via-powershell) или [Azure CLI](#deploy-via-azure-cli).

* При необходимости можно посетить коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) и развернуть шаблон из портал Azure.
* Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

> [!IMPORTANT]
>
> * При добавлении или удалении расположений в учетной записи Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны выполняться отдельно.
> * Длина имен учетных записей ограничена 44 символами, строчными буквами.
> * Чтобы изменить значения пропускной способности, повторно отправьте шаблон с обновленными единицами запросов в секунду.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Чтобы создать контейнер с большим ключом секции, измените предыдущий шаблон, включив в него свойство `"version":2` в объекте `partitionKey`.

### <a name="deploy-via-powershell"></a>Развертывание с помощью PowerShell

Чтобы использовать PowerShell для развертывания шаблона Azure Resource Manager, выполните следующие действия.

1. **Скопируйте** скрипт.
2. Выберите **попробовать** , чтобы открыть Azure Cloud Shell.
3. Щелкните правой кнопкой мыши в окне Azure Cloud Shell и выберите команду **Вставить**.

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

Вы можете развернуть шаблон с помощью локально установленной версии PowerShell вместо Azure Cloud Shell. Необходимо [установить модуль Azure PowerShell](/powershell/azure/install-az-ps). Запустите `Get-Module -ListAvailable Az`, чтобы найти требуемую версию.

### <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы использовать Azure CLI для развертывания шаблона Azure Resource Manager:

1. **Скопируйте** скрипт.
2. Выберите **попробовать** , чтобы открыть Azure Cloud Shell.
3. Щелкните правой кнопкой мыши в окне Azure Cloud Shell и выберите команду **Вставить**.

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

Команда `az cosmosdb show` отображает созданную учетную запись Azure Cosmos после ее подготовки. Можно выбрать развертывание шаблона с локально установленной версией Azure CLI вместо этого Azure Cloud Shell. Дополнительные сведения см. в статье [интерфейс командной строки Azure (CLI)](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Создание контейнера Azure Cosmos DB с функциональностью на стороне сервера

Шаблон Azure Resource Manager можно использовать для создания контейнера Azure Cosmos DB с хранимой процедурой, триггером и определяемой пользователем функцией.

Скопируйте приведенный ниже пример шаблона и разверните его, как описано в с помощью [PowerShell](#deploy-with-powershell) или [Azure CLI](#deploy-with-azure-cli).

* При необходимости вы можете посетить коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) и развернуть шаблон из портал Azure.
* Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

Чтобы использовать PowerShell для развертывания шаблона Azure Resource Manager, выполните следующие действия.

1. **Скопируйте** скрипт.
1. Выберите **попробовать** , чтобы открыть Azure Cloud Shell.
1. Щелкните правой кнопкой мыши окно Azure Cloud Shell и выберите команду **Вставить**.

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

Вы можете развернуть шаблон с помощью локально установленной версии PowerShell вместо Azure Cloud Shell. Необходимо [установить модуль Azure PowerShell](/powershell/azure/install-az-ps). Запустите `Get-Module -ListAvailable Az`, чтобы найти требуемую версию.

### <a name="deploy-with-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы использовать Azure CLI для развертывания шаблона Azure Resource Manager:

1. **Скопируйте** скрипт.
2. Выберите **попробовать** , чтобы открыть Azure Cloud Shell.
3. Щелкните правой кнопкой мыши в окне Azure Cloud Shell и выберите команду **Вставить**.

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
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
