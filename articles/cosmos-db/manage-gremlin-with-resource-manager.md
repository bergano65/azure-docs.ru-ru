---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB API Gremlin
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB Gremlin API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f50a6980cb26d6a89e2564b492a8be900ad57736
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606787"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами Azure Cosmos DB Gremlin API с помощью шаблонов Azure Resource Manager

В этой статье описывается, как выполнять различные операции для автоматизации Azure Cosmos DB управления учетными записями, базами данных и контейнерами с помощью шаблонов Azure Resource Manager. В этой статье приводятся примеры только для учетных записей API Gremlin. примеры для других учетных записей типа API см. в разделе Использование шаблонов диспетчер ресурсов с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и статей [таблиц](manage-table-with-resource-manager.md) .

## Создание Azure Cosmos DB API для учетной записи, базы данных и коллекции MongoDB<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для API Gremlin с двумя графами, которые совместно используют пропускную способность 400 единиц запросов/с на уровне базы данных. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

> [!NOTE]
> Имена учетных записей должны содержать строчные буквы и < 31 символ.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Команда `az cosmosdb show` отображает созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования CloudShell, см. статью [интерфейс командной строки Azure (CLI)](/cli/azure/) .

## Обновление пропускной способности (единиц запросов/с) в базе данных<a id="database-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность базы данных. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Развертывание шаблона базы данных с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Обновление пропускной способности (единиц запросов в секунду) на графе<a id="graph-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность графа. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Развертывание шаблона графа с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName graphName=$graphName throughput=$throughput
```

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)