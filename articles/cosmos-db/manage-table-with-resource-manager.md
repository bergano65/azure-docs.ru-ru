---
title: Шаблоны диспетчер ресурсов для Azure Cosmos DB API таблиц
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API таблиц.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d1675e6827f3684785d11ef6b081f166267a8283
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791193"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Управление Azure Cosmos DB API таблиц ресурсами с помощью шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания и управления учетными записями Azure Cosmos DB, базами данных и контейнерами.

В этой статье приведены примеры только для учетных записей API таблиц. чтобы найти примеры для других учетных записей типа API, см. статью использование шаблонов Azure Resource Manager с API Azure Cosmos DB для [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), статей [SQL](manage-sql-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами, строчными буквами.
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленными единицами запросов в секунду.
> * При добавлении или удалении расположений в учетной записи Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из Azure Cosmos DBных ниже ресурсов, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать файл параметров JSON, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Существует множество способов развертывания шаблонов Azure Resource Manager, включая [портал Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Чтобы включить общую пропускную способность при использовании API таблиц, включите пропускную способность на уровне учетной записи в портал Azure.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Учетная запись Azure Cosmos для таблицы с пропускной способностью автомасштабирования

Этот шаблон создаст учетную запись Azure Cosmos для API таблиц с одной таблицей с пропускной способностью автомасштабирования. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-manual-throughput"></a>Учетная запись Azure Cosmos для таблицы со стандартной пропускной способностью (вручную)

Этот шаблон создаст учетную запись Azure Cosmos для API таблиц с одной таблицей со стандартной пропускной способностью. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
