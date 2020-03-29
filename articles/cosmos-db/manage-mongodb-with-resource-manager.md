---
title: Шаблоны менеджера ресурсов для API DB Azure Cosmos для MongoDB
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки API DB Azure Cosmos dB для MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063636"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Управление aPI-ресурсами Azure Cosmos DB MongoDB с помощью шаблонов управления ресурсами Azure

В этой статье описывается, как выполнять различные операции для автоматизации управления учетными записями, базами данных и контейнерами Azure Cosmos, используя шаблоны Azure Resource Manager. В этой статье есть примеры aPI-изображаемого API Azure Cosmos DB только для MongoDB, чтобы найти примеры для других учетных записей типа API: используйте шаблоны управления ресурсами Azure Снайс dB для [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin](manage-gremlin-with-resource-manager.md), [S'L](manage-sql-with-resource-manager.md), [Таблица](manage-table-with-resource-manager.md) статей.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Создание API DB Azure Cosmos для учетной записи, базы данных и сбора MongoDB<a id="create-resource"></a>

Создавайте dB-ресурсы Azure Cosmos с помощью шаблона управления ресурсами Azure. Этот шаблон создаст учетную запись Azure Cosmos для API MongoDB с двумя коллекциями, которые имеют пропускную способности 400 RU/s на уровне базы данных. Копируйте шаблон и развертывайте, как показано ниже, или посетите [галерею быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) и разместите его с портала Azure. Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре и 44 или меньше символов.
> Чтобы обновить RU/s, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.
>
> В настоящее время можно создать только версию 3.2 (т.е. учетные записи, использующие конечную точку в формате) `*.documents.azure.com`API Azure Cosmos DB для учетных записей MongoDB с помощью PowerShell и CLI. Для создания 3.6 версии учетных записей используйте шаблоны «Менеджер ресурсов» (см. ниже) или портал Azure.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Развертывание через Azure CLI

Чтобы развернуть шаблон менеджера ресурсов Azure с помощью CLI Azure, **скопируйте** скрипт и **поберите «Попробуйте открыть** — облачную оболочку Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Команда `az cosmosdb show` показывает недавно созданную учетную запись Azure Cosmos после того, как она была подготовлена. Если вместо оболочки —сяра— местная версия CLI, установленная локально, смотрите статью [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Квикстарт](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение проблем общие ошибки в развертывании ресурсов Ресурсов Azure](../azure-resource-manager/templates/common-deployment-errors.md)
