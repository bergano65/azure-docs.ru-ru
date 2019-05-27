---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB Cassandra API
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB Cassandra API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: db754adbe60acfa155400910c47de556db793eef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968909"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами Azure Cosmos DB Cassandra API, с помощью шаблонов Azure Resource Manager

## Создание учетной записи Azure Cosmos, пространства ключей и таблицы <a id="create-resource"></a>

Создание ресурсов Azure Cosmos DB, с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для Cassandra API с двумя таблицами, которые совместно используют 400 ЕЗ/с пропускной способности на уровень пространства ключей. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон Resource Manager с помощью Azure CLI, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Команда показывает созданной учетной записи Azure Cosmos, после его подготовки. Если вы решили использовать локально установленная версия Azure CLI, а не CloudShell, см. в разделе [интерфейса командной строки Azure (CLI)](/cli/azure/) статьи.

## Обновить пропускную способность (ЕЗ/с) для пространства ключей <a id="keyspace-ru-update"></a>

Следующий шаблон обновит пропускную способность пространства ключей. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Развертывание шаблона пространства ключей с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Обновить пропускную способность (ЕЗ/с) для таблицы <a id="table-ru-update"></a>

Следующий шаблон обновит пропускную способность таблицы. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Развертывание шаблона таблицы с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Следующие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)