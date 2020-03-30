---
title: Шаблоны менеджера ресурсов для Azure Cosmos DB Cassandra API
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки Azure Cosmos DB Cassandra API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251899"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление API-ресурсами Azure Cosmos Cassandra API с помощью шаблонов управления ресурсами Azure

В этой статье описывается, как выполнять различные операции для автоматизации управления учетными записями, базами данных и контейнерами Azure Cosmos, используя шаблоны Azure Resource Manager. В этой статье есть примеры только для учетных записей API для S'L, чтобы найти примеры для других учетных записей типа API: используйте шаблоны менеджера ресурсов Azure Сресурса с API Azure Cosmos DB для [S'L](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Таблица](manage-table-with-resource-manager.md) статей.

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Создание учетной записи, пространства ключей и таблицы Azure Cosmos<a id="create-resource"></a>

Создавайте dB-ресурсы Azure Cosmos с помощью шаблона управления ресурсами Azure. Этот шаблон создаст учетную запись Azure Cosmos для API Cassandra с двумя таблицами, которые имеют пропускную способности 400 RU/s на уровне keyspace. Копируйте шаблон и развертывайте, как показано ниже, или посетите [галерею быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) и разместите его с портала Azure. Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре и 44 или меньше символов.
> Чтобы обновить RU/s, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон менеджера ресурсов Azure с помощью CLI Azure, **скопируйте** скрипт и **поберите «Попробуйте открыть** — облачную оболочку Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

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

Команда `az cosmosdb show` показывает недавно созданную учетную запись Azure Cosmos после того, как она была подготовлена. Если вместо оболочки —сяра— местная версия CLI, установленная локально, смотрите статью [Azure CLI.](/cli/azure/)


## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Квикстарт](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение проблем общие ошибки в развертывании ресурсов Ресурсов Azure](../azure-resource-manager/templates/common-deployment-errors.md)
