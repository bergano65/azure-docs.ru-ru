---
title: Примеры Azure PowerShell для Azure Cosmos DB
description: Примеры Azure PowerShell — это скрипты для создания учетных записей базы данных Azure Cosmos DB и управления ими.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 3498ac6a2a4aaa1682d7b5bc5aae5383866d5bcd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873803"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Примеры Azure PowerShell для базы данных Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры скриптов Azure PowerShell для базы данных Azure Cosmos DB. Сейчас с помощью PowerShell можно управлять только учетной записью Azure Cosmos DB. Другими ресурсами, например базами данных и контейнерами, нельзя управлять с помощью PowerShell.

| |  |
|---|---|
|**Создание учетной записи Azure Cosmos DB**||
|[Azure Cosmos DB — создание учетной записи API SQL с помощью PowerShell](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание одной учетной записи Azure Cosmos DB для использования с API SQL. |
|[Azure Cosmos DB — создание учетной записи API MongoDB с помощью PowerShell](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание одной учетной записи Cosmos с помощью API Azure Cosmos DB для MongoDB. |
|[Azure Cosmos DB — создание учетной записи API Gremlin с помощью PowerShell](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание одной учетной записи Azure Cosmos DB для использования с API Gremlin. |
|[Azure Cosmos DB — создание учетной записи API Cassandra с помощью PowerShell](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание одной учетной записи Azure Cosmos DB для использования с API Cassandra. |
|[Azure Cosmos DB — создание учетной записи API таблиц с помощью PowerShell](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание одной учетной записи Azure Cosmos DB для использования с API таблицы. |
|**Масштабирование Azure Cosmos DB**||
|[Replicate an Azure Cosmos DB database account in multiple regions and configure failover priorities using PowerShell](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Репликация учетной записи базы данных Azure Cosmos DB в нескольких регионах и настройка приоритетов отработки отказа с помощью PowerShell)|Глобально реплицирует данные учетной записи в нескольких регионах с указанным приоритетом отработки отказа.|
|**Безопасность базы данных Azure Cosmos DB**||
| [Получение ключей учетной записи](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Возвращение первичного и вторичного главных ключей для записи и первичного и вторичного ключей только для чтения для учетной записи.|
| [Получение строки подключения MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Получает строку подключения для подключения приложения MongoDB к учетной записи базы данных Azure Cosmos DB.|
|[Повторное создание ключей учетной записи](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Повторное создание главного ключа или ключа только для чтения для учетной записи.|
|[Создание брандмауэра](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание политики контроля доступа для входящих IP-адресов для разрешения доступа к учетной записи с утвержденных компьютеров и (или) из облачных служб.|
|**Высокий уровень доступности, аварийное восстановление, архивация и восстановление**||
|[Настройка политики отработки](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Задает приоритет отработки отказа каждого региона, в котором реплицируется учетная запись.|
|||
