---
title: Шаблоны Resource Manager для API Azure Cosmos DB для MongoDB
description: Использование шаблонов Azure Resource Manager для создания и настройки API Azure Cosmos DB для MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 1fe2c96e3c469e41e12d64f27a6a3b3d16a1174d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684775"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами API MongoDB Azure Cosmos DB с использованием шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания учетных записей, баз данных и контейнеров Azure Cosmos DB для API MongoDB и управления ими.

В этой статье приводятся примеры учетных записей только API Azure Cosmos DB для MongoDB. Примеры учетных записей других типов API см. в разделах об использовании шаблонов Azure Resource Manager с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-gremlin-with-resource-manager.md), [Gremlin](manage-sql-with-resource-manager.md) и [Table](manage-table-with-resource-manager.md).

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами (только строчные буквы).
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленным значением ЕЗ/с.
> * Одновременно с добавлением или удалением расположений в учетной записи Azure Cosmos нельзя изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из перечисленных ниже ресурсов Azure Cosmos DB, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать JSON-файл параметров, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Шаблоны Azure Resource Manager можно развертывать множеством способов, в том числе с помощью [портала Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для MongoDB с пропускной способностью, обеспечиваемой автомасштабированием

Этот шаблон служит для создания учетной записи Azure Cosmos для API MongoDB (3.2 или 3.6) с двумя коллекциями, которые совместно используют пропускную способность автомасштабирования на уровне базы данных. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для MongoDB со стандартной пропускной способностью

Этот шаблон служит для создания учетной записи Azure Cosmos для API MongoDB (3.2 или 3.6) с двумя коллекциями, которые совместно используют стандартную пропускную способность 400 ЕЗ/с (вручную) на уровне базы данных. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок при развертывании с помощью Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
