---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB API Cassandra
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: beae89b3f8e21e2f56a1dbf2090b7a612ed5cab0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815113"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление Azure Cosmos DB API Cassandra ресурсами с помощью шаблонов Azure Resource Manager

## Создание учетной записи Azure Cosmos, пространства ключей и таблицы<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для API Cassandra с двумя таблицами, которые совместно используют пропускную способность 400 единиц запросов/с на уровне пространства ключей. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью `--template-file` параметра.

> [!NOTE]
> Имена учетных записей должны содержать строчные буквы и < 31 символ.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

`az cosmosdb show` Команда отобразит созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования CloudShell, см. статью о [интерфейсе командной строки Azure (CLI)](/cli/azure/) .

## Обновление пропускной способности (единиц запросов в секунду) в пространства ключей<a id="keyspace-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность пространства ключей. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью `--template-file` параметра.

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Развертывание шаблона пространства ключей с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## Обновление пропускной способности (единиц запросов/с) в таблице<a id="table-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность таблицы. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью `--template-file` параметра.

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Развертывание шаблона таблицы с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
