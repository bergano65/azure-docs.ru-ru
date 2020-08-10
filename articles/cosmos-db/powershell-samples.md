---
title: Примеры Azure PowerShell для базы данных Azure Cosmos DB
description: Получение примеров Azure PowerShell для выполнения типичных задач в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505018"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Примеры Azure PowerShell для базы данных Azure Cosmos DB

В приведенной ниже таблице содержатся ссылки на популярные скрипты Azure PowerShell для Azure Cosmos DB. Щелкните ссылки справа, чтобы перейти к примерам API. Общие примеры одинаковы для всех API. Страницы справки по всем командлетам PowerShell Azure Cosmos DB доступны в [справочнике по Azure PowerShell](/powershell/module/az.cosmosdb). Регулярно проверяйте наличие обновлений для `Az.CosmosDB`. Если вы хотите создать вилки этих примеров PowerShell для Cosmos DB, посетите [страницу репозитория с примерами PowerShell для Cosmos DB на сайте GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Распространенные примеры

|Задача | Описание |
|---|---|
|[Обновление учетной записи](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление уровня согласованности по умолчанию для учетной записи Cosmos DB. |
|[Изменение регионов учетной записи](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменение регионов учетной записи Cosmos DB. |
|[Изменение приоритета отработки отказа или запуск отработки отказа](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменяет приоритет региональной отработки отказа для учетной записи Azure Cosmos или позволяет вручную запустить отработку отказа. |
|[Ключи или строки подключения учетной записи](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение первичного и вторичного ключей, строк подключения или воссоздание ключа учетной записи Azure Cosmos DB. |
|[Создание учетной записи Cosmos с правилами брандмауэра для IP-адресов](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos DB с активными правилами брандмауэра для IP-адресов. |
|||

## <a name="core-sql-api-samples"></a>Примеры API Core (SQL)

|Задача | Описание |
|---|---|
|[Создание учетной записи, базы данных и контейнера](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи, базы данных и контейнера Azure Cosmos DB |
|[Создание учетной записи, базы данных и контейнера с автомасштабированием](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи, базы данных и контейнера Azure Cosmos DB с автомасштабированием. |
|[Создание контейнера с ключом большого раздела](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает контейнер с ключом большого раздела. |
|[Создание контейнера без политики индексирования](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с отключенной политикой индексирования.|
|[Получение списка либо возврат баз данных или контейнеров](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат баз данных или контейнеров. |
|[Получение данных пропускной способности](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json).| Получение данных пропускной способности для базы данных или контейнера. |
|[Обновление данных пропускной способности](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление данных пропускной способности для базы данных или контейнера. |
|[Блокировка ресурсов от удаления](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||

## <a name="cassandra-api-samples"></a>Примеры API Cassandra

|Задача | Описание |
|---|---|
|[Создание учетной записи, пространства ключей и таблицы](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, пространства ключей и таблицы. |
|[Создание учетной записи, пространства ключей и таблицы с автомасштабированием](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, пространства ключей и таблицы с автомасштабированием. |
|[Получение списка либо возврат пространств ключей или таблиц](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат пространств ключей или таблиц. |
|[Получение данных пропускной способности](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json).| Получение данных пропускной способности для пространства ключей или таблицы. |
|[Обновление данных пропускной способности](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление данных пропускной способности для пространства ключей или таблицы. |
|[Блокировка ресурсов от удаления](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||

## <a name="mongo-db-api-samples"></a>Примеры API Mongo DB

|Задача | Описание |
|---|---|
|[Создание учетной записи, базы данных и коллекции](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, базы данных и коллекции. |
|[Создание учетной записи, базы данных и коллекции с автомасштабированием](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, базы данных и коллекции с автомасштабированием. |
|[Получение списка либо возврат баз данных или коллекций](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат баз данных или коллекций. |
|[Получение данных пропускной способности](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json).| Получение данных пропускной способности для базы данных или коллекции. |
|[Обновление данных пропускной способности](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление данных пропускной способности для базы данных или коллекции. |
|[Блокировка ресурсов от удаления](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||

## <a name="gremlin-api-samples"></a>Примеры API Gremlin

|Задача | Описание |
|---|---|
|[Создание учетной записи, базы данных и графа](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, базы данных и графа. |
|[Создание учетной записи, базы данных и графа с автомасштабированием](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, базы данных и графа с автомасштабированием. |
|[Получение списка либо возврат баз данных или графов](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат баз данных или графов. |
|[Получение данных пропускной способности](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json).| Получение данных пропускной способности для базы данных или графа. |
|[Обновление данных пропускной способности](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление данных пропускной способности для базы данных или графа. |
|[Блокировка ресурсов от удаления](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||

## <a name="table-api-samples"></a>Примеры API таблиц

|Задача | Описание |
|---|---|
|[Создание учетной записи и таблицы](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos и таблицы. |
|[Создание учетной записи и таблицы с автомасштабированием](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos и таблицы с автомасштабированием. |
|[Получение списка либо возврат таблиц](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат таблиц. |
|[Получение данных пропускной способности](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json).| Получение данных пропускной способности для таблицы. |
|[Обновление данных пропускной способности](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление данных пропускной способности для таблицы. |
|[Блокировка ресурсов от удаления](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||
