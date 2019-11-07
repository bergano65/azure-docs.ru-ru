---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB API таблиц
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API таблиц.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: aa169ed7001ad858a2a0373f9d5bbbe770a2a727
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604481"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Управление Azure Cosmos DB API таблиц ресурсами с помощью шаблонов Azure Resource Manager

В этой статье описывается, как выполнять различные операции для автоматизации Azure Cosmos DB управления учетными записями, базами данных и контейнерами с помощью шаблонов Azure Resource Manager. В этой статье приведены примеры только для учетных записей API таблиц. чтобы найти примеры для других учетных записей типа API, см. статью использование шаблонов диспетчер ресурсов с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), статей [SQL](manage-sql-with-resource-manager.md) .

## Создание учетной записи и таблицы Azure Cosmos<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для API таблиц с одной таблицей с пропускной способностью 400 единиц запросов в секунду. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

> [!NOTE]
> Имена учетных записей должны содержать строчные буквы и < 31 символ.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Развертывание с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Если вы решили использовать локально установленную версию PowerShell, а не Azure Cloud Shell, необходимо [установить](/powershell/azure/install-az-ps) модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Команда `az cosmosdb show` отображает созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования CloudShell, см. статью [интерфейс командной строки Azure (CLI)](/cli/azure/) .

## Обновление пропускной способности (единиц запросов/с) в таблице<a id="table-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность таблицы. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Развертывание пропускной способности таблицы с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Развертывание шаблона таблицы с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)