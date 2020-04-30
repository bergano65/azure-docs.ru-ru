---
title: Шаблоны диспетчер ресурсов для Azure Cosmos DB API Cassandra
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: ff75597bece386635195a84572a9f07b04d9c60f
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200821"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление Azure Cosmos DB API Cassandra ресурсами с помощью шаблонов Azure Resource Manager

В этой статье описывается, как выполнять различные операции для автоматизации Azure Cosmos DB управления учетными записями, базами данных и контейнерами с помощью шаблонов Azure Resource Manager. В этой статье приведены примеры только для учетных записей API Cassandra. чтобы найти примеры для других учетных записей типа API, см. статью использование шаблонов Azure Resource Manager с API Azure Cosmos DB для [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и статей [таблиц](manage-table-with-resource-manager.md) .

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Создание учетной записи Azure Cosmos, пространства ключей и таблицы<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для API Cassandra с двумя таблицами, которые совместно используют пропускную способность 400 единиц запросов/с на уровне пространства ключей. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью `--template-file` параметра.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре, 44 или меньше.
> Чтобы обновить единицы запросов в секунду, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "accountName": {
        "type": "string",
        "defaultValue": "",
        "metadata": {
            "description": "Cosmos DB account name, max length 44 characters"
        }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
            "description": "Location for the Cosmos DB account."
        }
    },
    "primaryRegion":{
        "type":"string",
        "metadata": {
            "description": "The primary replica region for the Cosmos DB account."
        }
    },
    "secondaryRegion":{
        "type":"string",
        "metadata": {
          "description": "The secondary replica region for the Cosmos DB account."
      }
    },
    "defaultConsistencyLevel": {
        "type": "string",
        "defaultValue": "Session",
        "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
        "metadata": {
            "description": "The default consistency level of the Cosmos DB account."
        }
    },
    "maxStalenessPrefix": {
        "type": "int",
        "defaultValue": 100000,
        "minValue": 10,
        "maxValue": 1000000,
        "metadata": {
            "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
        }
    },
    "maxIntervalInSeconds": {
        "type": "int",
        "defaultValue": 300,
        "minValue": 5,
        "maxValue": 86400,
        "metadata": {
            "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
        }
    },
    "automaticFailover": {
        "type": "bool",
        "defaultValue": true,
        "allowedValues": [ true, false ],
        "metadata": {
            "description": "Enable automatic failover for regions"
        }
    },
    "keyspaceName": {
        "type": "string",
        "defaultValue": "Keyspace1",
        "metadata": {
            "description": "The name for the Cassandra Keyspace"
        }
    },
    "tableName": {
        "type": "string",
        "defaultValue": "Table1",
        "metadata": {
            "description": "The name for the Cassandra table"
        }
    },
    "throughput": {
        "type": "int",
        "defaultValue": 400,
        "minValue": 400,
        "maxValue": 1000000,
        "metadata": {
            "description": "The throughput for the Cassandra table"
        }
    }
},
"variables": {
    "accountName": "[toLower(parameters('accountName'))]",
    "consistencyPolicy": {
        "Eventual": {
            "defaultConsistencyLevel": "Eventual"
        },
        "ConsistentPrefix": {
            "defaultConsistencyLevel": "ConsistentPrefix"
        },
        "Session": {
            "defaultConsistencyLevel": "Session"
        },
        "BoundedStaleness": {
            "defaultConsistencyLevel": "BoundedStaleness",
            "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
            "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
        },
        "Strong": {
            "defaultConsistencyLevel": "Strong"
        }
    },
    "locations":
    [
        {
            "locationName": "[parameters('primaryRegion')]",
            "failoverPriority": 0,
            "isZoneRedundant": false
        },
        {
            "locationName": "[parameters('secondaryRegion')]",
            "failoverPriority": 1,
            "isZoneRedundant": false
        }
    ]
},
"resources":
[
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-03-01",
        "location": "[parameters('location')]",
        "kind": "GlobalDocumentDB",
        "properties": {
            "capabilities": [{ "name": "EnableCassandra" }],
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]"
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
        "properties":{
            "resource":{
                "id": "[parameters('keyspaceName')]"
            }
        }
    },
    {
        "type": "Microsoft.DocumentDb/databaseAccounts/cassandraKeyspaces/tables",
        "name": "[concat(variables('accountName'), '/', parameters('keyspaceName'), '/', parameters('tableName'))]",
        "apiVersion": "2020-03-01",
        "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/cassandraKeyspaces', variables('accountName'), parameters('keyspaceName'))]" ],
        "properties":
        {
            "resource":{
                "id":  "[parameters('tableName')]",
                "schema": {
                    "columns": [
                        { "name": "loadid", "type": "uuid" },
                        { "name": "machine", "type": "uuid" },
                        { "name": "cpu", "type": "int" },
                        { "name": "mtime", "type": "int" },
                        { "name": "load", "type": "float" }
                    ],
                    "partitionKeys": [
                        { "name": "machine" },
                        { "name": "cpu" },
                        { "name": "mtime" }
                    ],
                    "clusterKeys": [
                        { "name": "loadid", "orderBy": "asc" }
                    ]
                },
                "options": { "throughput": "[parameters('throughput')]" }
            }
        }
    }
]
}
```

## <a name="deploy-with-the-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон Azure Resource Manager с помощью Azure CLI, **скопируйте** скрипт и выберите **попробовать** открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keyspaceName=$keyspaceName \
   tableName=$tableName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Команда отобразит созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования Cloud Shell, см. статью [Azure CLI](/cli/azure/) .

## <a name="next-steps"></a>Дальнейшие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
