---
title: Примеры Azure CLI для API SQL (Core) Azure Cosmos DB
description: Примеры Azure CLI для API SQL (Core) Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 46536dcaf5e8565bb621fa3b1c3ff547459816ab
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509429"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Примеры Azure CLI для API Azure Cosmos DB Core (SQL)

В следующей таблице содержатся ссылки на примеры скриптов Azure CLI для API Azure Cosmos DB Core (SQL). Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](/cli/azure/cosmosdb). Примеры использования Azure CLI для других API Azure Cosmos DB см. в статьях об [API Cassandra](cli-samples-cassandra.md), [API MongoDB](cli-samples-mongodb.md), [API Gremlin](cli-samples-gremlin.md) и [API таблиц](cli-samples-table.md). Все примеры сценариев Azure Cosmos DB CLI можно найти в [этом репозитории на сайте GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

| |  |
|---|---|
| [Создание учетной записи Azure Cosmos, базы данных и контейнера](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и контейнер Azure Cosmos DB для API SQL (Core). |
| [Изменение пропускной способности](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Обновляет ЕЗ/с в базе данных или контейнере.|
| [Добавление регионов или отработка отказа в регионах](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Добавляет регион, изменяет приоритет для отработки отказа и активирует переход на другой ресурс вручную.|
| [Ключи и строки подключения учетной записи](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Выводит ключи учетной записи, ключи только для чтения, повторно создает ключи и выводит строки подключения.|
| [Защита с помощью брандмауэра IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos с настроенным брандмауэром для IP-адресов.|
| [Защита новой учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos и обеспечивает безопасность с помощью конечных точек службы.|
| [Защита имеющейся учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Обновляет учетную запись Cosmos для защиты с помощью конечных точек службы после настройки подсети.|
| [Блокировка ресурсов от удаления](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||
