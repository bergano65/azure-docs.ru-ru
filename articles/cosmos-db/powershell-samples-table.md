---
title: Примеры Azure PowerShell для Azure Cosmos DB. API таблиц
description: Получение примеров Azure PowerShell для выполнения разных типичных задач в учетных записях API таблиц Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 751fa9daabaaef05abb5df8229e84975b87cffc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563834"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---table-api"></a>Примеры Azure PowerShell для Azure Cosmos DB. API таблиц

В приведенной ниже таблице содержатся ссылки на примеры скриптов Azure PowerShell для Azure Cosmos DB для API таблиц.

> [!NOTE]
> В примерах используются командлеты управления [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Регулярно проверяйте наличие обновлений для `Az.CosmosDB`.

|Задача | Описание |
|---|---|
|[Создание учетной записи и таблицы](scripts/powershell/table/ps-table-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание учетной записи Azure Cosmos и таблицы. |
|[Получение списка либо возврат таблиц](scripts/powershell/table/ps-table-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение списка либо возврат таблиц. |
|[Получение показателя ЕЗ/с](scripts/powershell/table/ps-table-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получение показателя ЕЗ/с для таблицы. |
|[Обновление ЕЗ/с](scripts/powershell/table/ps-table-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Обновление ЕЗ/с для таблицы. |
|[Обновление учетной записи или добавление региона](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Добавляет регион в учетную запись Cosmos. Также может использоваться для изменения других свойств учетной записи, но они должны вноситься отдельно от изменений регионов. |
|[Изменение приоритета отработки отказа или запуск отработки отказа](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Изменяет приоритет региональной отработки отказа для учетной записи Azure Cosmos или позволяет вручную запустить отработку отказа. |
|[Ключи или строки подключения учетной записи](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Получает первичный и вторичный ключи, строки подключения или воссоздает ключ учетной записи для учетной записи Azure Cosmos. |
|[Создание учетной записи Cosmos с правилами брандмауэра для IP-адресов](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает учетную запись Azure Cosmos с активными правилами брандмауэра для IP-адресов. |
|[Блокировка ресурсов от удаления](scripts/powershell/table/powershell-table-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Запрет на удаление ресурсов с помощью блокировок ресурсов. |
|||
