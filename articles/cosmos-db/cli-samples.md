---
title: Примеры Azure CLI для Azure Cosmos DB
description: Примеры Azure CLI для Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58460dffad3d44090644a544f4082b7727ece3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840342"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Примеры Azure CLI для Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры сценариев Azure CLI для Azure Cosmos DB. Щелкните ссылки справа, чтобы перейти к примерам API. Общие примеры одинаковы для всех API. Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](/cli/azure/cosmosdb). Примеры сценариев Azure Cosmos DB CLI можно также найти в [этом репозитории на сайте GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Для выполнения этих примеров требуется Azure CLI версии 2.9.1 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, ознакомьтесь со статьей [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="common-samples"></a>Распространенные примеры

Эти примеры применимы ко всем интерфейсам API Azure Cosmos DB.

|Задача | Описание |
|---|---|
| [Добавление регионов или отработка отказа в регионах](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Добавляет регион, изменяет приоритет для отработки отказа и активирует переход на другой ресурс вручную.|
| [Ключи и строки подключения учетной записи](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Выводит ключи учетной записи, ключи только для чтения, повторно создает ключи и выводит строки подключения.|
| [Защита с помощью брандмауэра IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos с настроенным брандмауэром для IP-адресов.|
| [Защита новой учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos и обеспечивает безопасность с помощью конечных точек службы.|
| [Защита имеющейся учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Обновляет учетную запись Cosmos для защиты с помощью конечных точек службы после настройки подсети.|
|||

## <a name="core-sql-api-samples"></a>Примеры API Core (SQL)

|Задача | Описание |
|---|---|
| [Создание учетной записи Azure Cosmos, базы данных и контейнера](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и контейнер Azure Cosmos DB для API Core (SQL). |
| [Создание учетной записи, базы данных и контейнера Azure Cosmos с автомасштабированием](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Azure Cosmos DB, базу данных и контейнер Azure Cosmos DB с автомасштабированием для API Core (SQL). |
| [Операции с пропускной способностью](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Чтение и обновление значений пропускной способности, а также переход между автомасштабируемой и стандартной пропускной способностью базы данных и контейнера.|
| [Блокировка ресурсов от удаления](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||

## <a name="mongodb-api-samples"></a>Примеры API MongoDB

|Задача | Описание |
|---|---|
| [Создание учетной записи Azure Cosmos, базы данных и коллекции](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и коллекцию API MongoDB в Azure Cosmos DB. |
| [Создание учетной записи и базы данных Azure Cosmos с автомасштабированием, а также двух коллекций c общей пропускной способностью](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись и базу данных Azure Cosmos DB с автомасштабированием, а также две коллекции c общей пропускной способностью для API MongoDB. |
| [Операции с пропускной способностью](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Чтение и обновление значений пропускной способности, а также переход между автомасштабируемой и стандартной пропускной способностью базы данных и коллекции.|
| [Блокировка ресурсов от удаления](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||

## <a name="cassandra-api-samples"></a>Примеры API Cassandra

|Задача | Описание |
|---|---|
| [Создание учетной записи Azure Cosmos, пространства ключей и таблицы](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Azure Cosmos DB, пространство ключей и таблицу для API Cassandra. |
| [Создание учетной записи, пространства ключей и таблицы Azure Cosmos с автомасштабированием](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, пространство ключей и таблицу Azure Cosmos DB с автомасштабированием для API Cassandra. |
| [Операции с пропускной способностью](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Чтение и обновление значений пропускной способности, а также переход между автомасштабируемой и стандартной пропускной способностью пространства ключей и таблицы.|
| [Блокировка ресурсов от удаления](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||

## <a name="gremlin-api-samples"></a>Примеры API Gremlin

|Задача | Описание |
|---|---|
| [Создание учетной записи Azure Cosmos, базы данных и графа](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и граф для API Gremlin в Azure Cosmos DB. |
| [Создание учетной записи, базы данных и графа Azure Cosmos с применением автомасштабирования](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и граф Azure Cosmos DB с применением автомасштабирования для API Gremlin. |
| [Операции с пропускной способностью](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Чтение и обновление значений пропускной способности, а также переход между автомасштабируемой и стандартной пропускной способностью базы данных и графа.|
| [Блокировка ресурсов от удаления](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||

## <a name="table-api-samples"></a>Примеры API таблиц

|Задача | Описание |
|---|---|
| [Создание учетной записи Azure Cosmos и таблицы](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Azure Cosmos DB и таблицу для API таблиц. |
| [Создание учетной записи и таблицы Azure Cosmos с применением автомасштабирования](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись и таблицу Azure Cosmos DB для API таблиц. |
| [Операции с пропускной способностью](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Чтение и обновление значений пропускной способности, а также переход между автомасштабируемой и стандартной пропускной способностью таблицы.|
| [Блокировка ресурсов от удаления](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||
