---
title: Примеры Azure PowerShell для Azure Cosmos DB. API MongoDB
description: Получение примеров Azure PowerShell для выполнения разных типичных задач в API Azure Cosmos DB для MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366197"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Примеры Azure PowerShell для Azure Cosmos DB с API MongoDB

В приведенной ниже таблице содержатся ссылки на примеры скриптов Azure PowerShell для Azure Cosmos DB для API MongoDB.

> [!NOTE]
> Сейчас можно создать только версию 3,2 (то есть ту, в которой для учетных записей используется конечная точка в формате `*.documents.azure.com`) приложения Azure Cosmos DB для учетных записей MongoDB с помощью PowerShell, интерфейса командной строки и шаблонов диспетчера ресурсов. Чтобы создать версию учетной записи 3.6, используйте портал Azure.

> [!NOTE]
> В примерах используются командлеты управления [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Обратите внимание, что командлеты `Az.CosmosDB` пока доступны в предварительной версии и могут быть изменены до выхода общедоступной версии. Регулярно проверяйте наличие обновлений для `Az.CosmosDB`.

| | |
|---|---|
|[Создание учетной записи, базы данных и коллекции](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos, базы данных и коллекции. |
|[Получение списка либо возврат баз данных или коллекций](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат баз данных или коллекций. |
|[Получение показателя ЕЗ/с](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение показателя ЕЗ/с для базы данных или коллекции. |
|[Обновление ЕЗ/с](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление ЕЗ/с для базы данных или коллекции. |
|[Обновление учетной записи или добавление региона](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Добавляет регион в учетную запись Cosmos. Также может использоваться для изменения других свойств учетной записи, но они должны вноситься отдельно от изменений регионов. |
|[Изменение приоритета отработки отказа или запуск отработки отказа](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменяет приоритет региональной отработки отказа для учетной записи Azure Cosmos или позволяет вручную запустить отработку отказа. |
|[Ключи или строки подключения учетной записи](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает первичный и вторичный ключи, строки подключения или воссоздает ключ учетной записи для учетной записи Azure Cosmos. |
|[Создание учетной записи Cosmos с правилами брандмауэра для IP-адресов](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает учетную запись Azure Cosmos с активными правилами брандмауэра для IP-адресов. |
|||
