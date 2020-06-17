---
title: Примеры Azure CLI для API MongoDB в Azure Cosmos DB
description: Примеры Azure CLI для API MongoDB в Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: af510e5d29e494e96f6df6143fe012db20fb6ca4
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509463"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Примеры Azure CLI для API MongoDB в Azure Cosmos DB

В следующей таблице содержатся ссылки на примеры скриптов Azure CLI для API MongoDB в Azure Cosmos DB. Страницы справки для всех команд интерфейса командной строки Azure Cosmos DB доступны в [справочнике по Azure CLI](/cli/azure/cosmosdb). Все примеры сценариев Azure Cosmos DB CLI можно найти в [этом репозитории на сайте GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Сейчас можно создать только версию 3,2 (то есть ту, в которой для учетных записей используется конечная точка в формате `*.documents.azure.com`) приложения Azure Cosmos DB для учетных записей MongoDB с помощью PowerShell, интерфейса командной строки и шаблонов диспетчера ресурсов. Чтобы создать версию учетной записи 3.6, используйте портал Azure.

| |  |
|---|---|
| [Создание учетной записи Azure Cosmos, базы данных и коллекции](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись, базу данных и коллекцию API MongoDB в Azure Cosmos DB. |
| [Изменение пропускной способности](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Обновляет ЕЗ/с в базе данных и коллекции.|
| [Добавление регионов или отработка отказа в регионах](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Добавляет регион, изменяет приоритет для отработки отказа и активирует переход на другой ресурс вручную.|
| [Ключи и строки подключения учетной записи](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Выводит ключи учетной записи, ключи только для чтения, повторно создает ключи и выводит строки подключения.|
| [Защита с помощью брандмауэра IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos с настроенным брандмауэром для IP-адресов.|
| [Защита новой учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Создает учетную запись Cosmos и обеспечивает безопасность с помощью конечных точек службы.|
| [Защита имеющейся учетной записи с помощью конечных точек службы](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Обновляет учетную запись Cosmos для защиты с помощью конечных точек службы после настройки подсети.|
| [Блокировка ресурсов от удаления](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов.|
|||
