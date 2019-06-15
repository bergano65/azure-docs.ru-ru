---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB Gremlin API
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB Gremlin API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 9f62399e3a1ef2a4ceaa8bdf64196bdb634fb4b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968890"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами Azure Cosmos DB Gremlin API, с помощью шаблонов Azure Resource Manager

## Создание учетной записи MongoDB, базы данных и коллекции Azure Cosmos DB API <a id="create-resource"></a>

Создание ресурсов Azure Cosmos DB, с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для Gremlin API с двумя графиками, совместно использующих 400 ЕЗ/с пропускной способности на уровне базы данных. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон Resource Manager с помощью Azure CLI, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

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

`az cosmosdb show` Команда показывает созданной учетной записи Azure Cosmos, после его подготовки. Если вы решили использовать локально установленная версия Azure CLI, а не CloudShell, см. в разделе [интерфейса командной строки Azure (CLI)](/cli/azure/) статьи.

## Обновить пропускную способность (ЕЗ/с) в базе данных <a id="database-ru-update"></a>

Следующий шаблон обновит пропускной способности базы данных. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Развертывание шаблона базы данных с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Обновить пропускную способность (ЕЗ/с) на диаграмме <a id="graph-ru-update"></a>

Следующий шаблон обновит пропускную способность графа. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Развертывание шаблона graph с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

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

## <a name="next-steps"></a>Следующие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)