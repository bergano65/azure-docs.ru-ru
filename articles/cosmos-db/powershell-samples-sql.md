---
title: Примеры Azure PowerShell для Azure Cosmos DB — API SQL (Core)
description: Получение примеров Azure PowerShell для выполнения разных типичных задач в учетных записях API SQL Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 7cd14151d8cb0fbef7593d62eee050be7987ab18
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713545"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Примеры Azure PowerShell для Azure Cosmos DB — API SQL (Core)

В приведенной ниже таблице содержатся ссылки на популярные скрипты Azure PowerShell для Azure Cosmos DB для API SQL (Core). Если вы хотите создать вилки этих примеров PowerShell для Cosmos DB, посетите [страницу репозитория с примерами PowerShell для Cosmos DB на сайте GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Дополнительные примеры использования PowerShell в Cosmos DB для API SQL (Core) и документацию можно найти на странице [Управление ресурсами API SQL для Azure Cosmos DB с помощью PowerShell](manage-with-powershell.md). Примеры использования PowerShell в Cosmos DB для других API см. в статьях о [API Cassandra](powershell-samples-cassandra.md), [API MongoDB](powershell-samples-mongodb.md), [API Gremlin](powershell-samples-gremlin.md) и [API таблиц](powershell-samples-table.md).

> [!NOTE]
> В примерах используются командлеты управления [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Регулярно проверяйте наличие обновлений для `Az.CosmosDB`.

| | |
|---|---|
|[Создание учетной записи, базы данных и контейнера](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи, базы данных и контейнера Azure Cosmos DB |
|[Создание контейнера с ключом большого раздела](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает контейнер с ключом большого раздела. |
|[Получение списка либо возврат баз данных или контейнеров](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат баз данных или контейнеров. |
|[Получение показателя ЕЗ/с](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает ЕЗ/с для базы данных или контейнера. |
|[Обновление ЕЗ/с](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление ЕЗ/с для базы данных или контейнера. |
|[Создание контейнера без политики индексирования](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает контейнер Azure Cosmos с отключенной политикой индексирования.|
|[Обновление учетной записи](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление уровня согласованности по умолчанию для учетной записи Cosmos DB. |
|[Изменение регионов учетной записи](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменение регионов учетной записи Cosmos DB. |
|[Изменение приоритета отработки отказа или запуск отработки отказа](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменяет приоритет региональной отработки отказа для учетной записи Azure Cosmos или позволяет вручную запустить отработку отказа. |
|[Ключи или строки подключения учетной записи](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение первичного и вторичного ключей, строк подключения или воссоздание ключа учетной записи Azure Cosmos DB. |
|[Создание учетной записи Cosmos с правилами брандмауэра для IP-адресов](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos DB с активными правилами брандмауэра для IP-адресов. |
|||
