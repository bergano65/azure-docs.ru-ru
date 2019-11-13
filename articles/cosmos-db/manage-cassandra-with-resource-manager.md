---
title: Шаблоны Azure Resource Manager для Azure Cosmos DB API Cassandra
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: f09ab6958eb768895a2d16f129354074cb6f00c8
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961873"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление Azure Cosmos DB API Cassandra ресурсами с помощью шаблонов Azure Resource Manager

В этой статье описывается, как выполнять различные операции для автоматизации Azure Cosmos DB управления учетными записями, базами данных и контейнерами с помощью шаблонов Azure Resource Manager. В этой статье приводятся примеры только для учетных записей API SQL. примеры для других учетных записей типа API см. в разделе Использование шаблонов Azure Resource Manager с API Azure Cosmos DB для [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и статей [таблиц](manage-table-with-resource-manager.md) .

## Создание учетной записи Azure Cosmos, пространства ключей и таблицы<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos для API Cassandra с двумя таблицами, которые совместно используют пропускную способность 400 единиц запросов/с на уровне пространства ключей. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре, 44 или меньше.
> Чтобы обновить единицы запросов в секунду, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-the-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон Azure Resource Manager с помощью Azure CLI, **скопируйте** скрипт и выберите **попробовать** открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

Команда `az cosmosdb show` отображает созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования Cloud Shell, см. статью [Azure CLI](/cli/azure/) .


## <a name="next-steps"></a>Дополнительная информация

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
