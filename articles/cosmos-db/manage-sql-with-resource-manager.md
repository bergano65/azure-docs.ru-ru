---
title: Создание Azure Cosmos DB с помощью шаблонов диспетчер ресурсов и управление ими
description: Использование шаблонов Azure Resource Manager для создания и настройки Azure Cosmos DB для API ядра (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 577bc34e5e4b01a234460e5e175c23fd8215743f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791210"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Управление ресурсами API Azure Cosmos DB Core (SQL) с помощью шаблонов Azure Resource Manager

Из этой статьи вы узнаете, как использовать шаблоны Azure Resource Manager для развертывания и управления учетными записями Azure Cosmos DB, базами данных и контейнерами.

В этой статье показаны Azure Resource Manager примеры шаблонов для учетных записей API ядра (SQL). Вы также можете найти примеры шаблонов для API-интерфейсов [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)и [Table](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Длина имен учетных записей ограничена 44 символами, строчными буквами.
> * Чтобы изменить значения пропускной способности, повторно разверните шаблон с обновленными единицами запросов в секунду.
> * При добавлении или удалении расположений в учетной записи Azure Cosmos нельзя одновременно изменять другие свойства. Эти операции должны выполняться отдельно.

Чтобы создать любой из Azure Cosmos DBных ниже ресурсов, скопируйте следующий пример шаблона в новый JSON-файл. При необходимости можно создать файл параметров JSON, который будет использоваться при развертывании нескольких экземпляров одного ресурса с разными именами и значениями. Существует множество способов развертывания шаблонов Azure Resource Manager, включая [портал Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) и [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Учетная запись Azure Cosmos с пропускной способностью автомасштабирования

Этот шаблон создает учетную запись Azure Cosmos в двух регионах с параметрами согласованности и отработки отказа, при этом база данных и контейнер настроены для пропускной способности автомасштабирования, в которой включены большинство параметров политики. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-manual-throughput"></a>Учетная запись Azure Cosmos со стандартной (ручной) пропускной способностью

Этот шаблон создает учетную запись Azure Cosmos в двух регионах с параметрами согласованности и отработки отказа, при этом база данных и контейнер настроены для стандартной пропускной способности, в которой включены большинство параметров политики. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB контейнер с функциональностью на стороне сервера

Этот шаблон создает учетную запись Azure Cosmos, базу данных и контейнер с хранимой процедурой, триггером и определяемой пользователем функцией. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Бесплатная учетная запись Azure Cosmos DB уровня

Этот шаблон создает бесплатную учетную запись Azure Cosmos и базу данных с общей пропускной способностью, которую можно совместно использовать до 25 контейнеров. Этот шаблон также доступен для развертывания одним щелчком из коллекции шаблонов быстрого запуска Azure.

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

* [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
* [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
