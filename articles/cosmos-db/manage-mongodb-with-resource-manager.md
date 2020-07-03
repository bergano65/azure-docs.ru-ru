---
title: Шаблоны диспетчер ресурсов для Azure Cosmos DB API для MongoDB
description: Используйте шаблоны Azure Resource Manager, чтобы создать и настроить Azure Cosmos DB API для MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d6f916002f949f78e4854903940f342261a109ff
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791227"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами Azure Cosmos DB MongoDB API с помощью шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания и управления учетными записями Azure Cosmos DB для API-интерфейсов, баз данных и коллекций MongoDB.

В этой статье приводятся примеры для API Azure Cosmos DB только для MongoDB, чтобы найти примеры для других учетных записей типа API. см. раздел Использование шаблонов Azure Resource Manager с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), а также статей [таблиц](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами, строчными буквами.
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленными единицами запросов в секунду.
> * При добавлении или удалении расположений в учетной записи Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из Azure Cosmos DBных ниже ресурсов, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать файл параметров JSON, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Существует множество способов развертывания шаблонов Azure Resource Manager, включая [портал Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Учетная запись Azure Cosmos для MongoDB с подготовленной пропускной способностью автомасштабирования

Этот шаблон создаст учетную запись Azure Cosmos для API MongoDB (3,2 или 3,6) с двумя коллекциями, которые совместно используют пропускную способность автомасштабирования на уровне базы данных. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Учетная запись Azure Cosmos для MongoDB со стандартной подготовленной пропускной способностью

Этот шаблон создаст учетную запись Azure Cosmos для API MongoDB (3,2 или 3,6) с двумя коллекциями, которые совместно 400 используют пропускную способность Standard (вручную) на уровне базы данных. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
