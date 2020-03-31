---
title: Шаблоны менеджера ресурсов для API Azure Cosmos DB Gremlin
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки API Azure Cosmos DB Gremlin.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e4c18d93f07cb2143dcc5bf9c93b9ac7298d2f7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246751"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Управление aPI-ресурсами Azure Cosmos DB Gremlin с помощью шаблонов управления ресурсами Azure

В этой статье описывается, как выполнять различные операции для автоматизации управления учетными записями, базами данных и контейнерами Azure Cosmos, используя шаблоны Azure Resource Manager. В этой статье есть примеры только для учетных записей Gremlin API, чтобы найти примеры для других учетных записей типа API: используйте шаблоны менеджера ресурсов Azure Саус с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [S'L](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Таблица](manage-table-with-resource-manager.md) статей.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Создание API DB Azure Cosmos для учетной записи, базы данных и сбора MongoDB<a id="create-resource"></a>

Создавайте dB-ресурсы Azure Cosmos с помощью шаблона управления ресурсами Azure. Этот шаблон создаст учетную запись Azure Cosmos для API Gremlin с двумя графиками, которые имеют пропускную способности 400 RU/s на уровне базы данных. Копируйте шаблон и развертывайте, как показано ниже, или посетите [галерею быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) и разместите его с портала Azure. Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре и 44 или меньше символов.
> Чтобы обновить RU/s, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон менеджера ресурсов Azure с помощью CLI Azure, **скопируйте** скрипт и **поберите «Попробуйте открыть** — облачную оболочку Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

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

Команда `az cosmosdb show` показывает недавно созданную учетную запись Azure Cosmos после того, как она была подготовлена. Если вместо оболочки —сяра— местная версия CLI, установленная локально, смотрите статью [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Квикстарт](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение проблем общие ошибки в развертывании ресурсов Ресурсов Azure](../azure-resource-manager/templates/common-deployment-errors.md)