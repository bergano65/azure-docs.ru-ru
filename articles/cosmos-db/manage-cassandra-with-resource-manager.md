---
title: Шаблоны Resource Manager для API Cassandra Azure Cosmos DB
description: Использование шаблонов Azure Resource Manager для создания и настройки API Cassandra Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 630c965eadf17e21c75e1bd180fec4496df830e2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684828"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами API Cassandra Azure Cosmos DB с использованием шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания учетных записей, пространств ключей и таблиц Azure Cosmos DB и управлять ими.

В этой статье приводятся примеры только для учетных записей API Cassandra. Примеры для учетных записей других типов API см. в разделах об использовании шаблонов Azure Resource Manager с API Azure Cosmos DB для [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md) и [Table](manage-table-with-resource-manager.md).

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами (только строчные буквы).
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленным значением ЕЗ/с.
> * Одновременно с добавлением или удалением расположений в учетной записи Azure Cosmos нельзя изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из перечисленных ниже ресурсов Azure Cosmos DB, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать JSON-файл параметров, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Шаблоны Azure Resource Manager можно развертывать множеством способов, в том числе с помощью [портала Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для Cassandra с пропускной способностью, обеспечиваемой автомасштабированием

Этот шаблон используется, чтобы создать учетную запись Azure Cosmos в двух регионах с заданными параметрами для согласованности и отработки отказа. При этом пространство ключей и таблица настроены для автомасштабирования пропускной способности. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для Cassandra со стандартной пропускной способностью

Этот шаблон используется, чтобы создать учетную запись Azure Cosmos в двух регионах с заданными параметрами для согласованности и отработки отказа. При этом пространство ключей и таблица настроены на стандартную пропускную способность. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок при развертывании с помощью Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
