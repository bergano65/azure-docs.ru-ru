---
title: Шаблоны диспетчер ресурсов для Azure Cosmos DB API Gremlin
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB Gremlin API.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: defeec2317f47e9d79245cbde0ef81c8a3543bfb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791244"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами Azure Cosmos DB Gremlin API с помощью шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания и управления учетными записями Azure Cosmos DB, базами данных и графами.

В этой статье приводятся примеры только для учетных записей API Gremlin. примеры для других учетных записей типа API см. в разделе Использование шаблонов Azure Resource Manager с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и статей [таблиц](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами, строчными буквами.
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленными единицами запросов в секунду.
> * При добавлении или удалении расположений в учетной записи Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из Azure Cosmos DBных ниже ресурсов, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать файл параметров JSON, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Существует множество способов развертывания шаблонов Azure Resource Manager, включая [портал Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для Gremlin с подготовленной пропускной способностью автомасштабирования

Этот шаблон создаст учетную запись Azure Cosmos для API Gremlin с базой данных и графом с пропускной способностью автомасштабирования.

Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Учетная запись Azure Cosmos DB для Gremlin со стандартной подготовленной пропускной способностью

Этот шаблон создаст учетную запись Azure Cosmos для API Gremlin с базой данных и графом с обычной пропускной способностью (вручную).

Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)