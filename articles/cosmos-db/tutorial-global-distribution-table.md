---
title: Руководство по настройке глобального распределения Azure Cosmos DB с помощью API таблиц | Документация Майкрософт
description: Сведения о настройке глобального распределения Azure Cosmos DB с помощью API таблицы.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2018
ms.reviewer: sngun
ms.openlocfilehash: 8562d37d81ce02e150e6ad1cc2a440cf7bb1e5e3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693361"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Настройка глобального распределения Azure Cosmos DB с помощью API таблицы

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настройка глобального распределения на портале Azure;
> * настройка глобального распределения с помощью [API таблицы](table-introduction.md).

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Подключение к предпочтительному региону с помощью API таблицы

Чтобы воспользоваться преимуществами [глобального распределения](distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, задав свойство [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). Пакет SDK для API таблиц в Azure Cosmos DB выбирает лучшую конечную точку для взаимодействия на основе конфигурации учетной записи, текущей доступности региона и указанного списка предпочтений.

Список PreferredLocations должен содержать разделенные запятыми предпочтительные расположения (множественная адресация) для операций чтения. Каждый экземпляр клиента может указать подмножество этих регионов в порядке предпочтения, чтобы обеспечить низкую задержку операций чтения. Регионам необходимо присвоить их [отображаемые имена](https://msdn.microsoft.com/library/azure/gg441293.aspx), например `West US`.

Все операции чтения отправляются в первый доступный регион из списка PreferredLocations. Если запрос завершится неудачей, клиент перейдет к следующему региону дальше по списку и так далее.

Пакеты SDK пытаются выполнять чтение данных из регионов, указанных в списке PreferredLocations. Например, если учетная запись базы данных доступна в трех регионах, но клиент указывает в PreferredLocations только два региона не для записи, то операции чтения из региона записи не будут обслуживаться даже в случае отработки отказа.

Пакет SDK автоматически отправляет все операции записи в текущий регион записи.

Если свойство PreferredLocations не задано, будут обслуживаться все запросы из текущего региона записи.

На этом руководство завершено. Сведения об управлении согласованностью глобально реплицируемой учетной записи Azure Cosmos DB см. в [этой статье](consistency-levels.md). Сведения о том, как функционирует репликация глобальной базы данных в Azure Cosmos DB, см. в статье о [глобальном распределении данных в Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью API таблиц в Azure Cosmos DB.

