---
title: Создание блокировки ресурса для таблицы API таблицы БД Azure Cosmos
description: Создание блокировки ресурса для таблицы API таблицы БД Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: e3f6af81b43844448463c9430c1ab9dd52f5703f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075880"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Создание блокировки ресурса для таблицы API таблицы БД Azure Cosmos с помощью Azure CLI
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.9.1 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Блокировки ресурсов не работают для изменений, внесенных пользователями, которые подключаются с помощью пакета Cosmos DB Table SDK, Azure Storage Table SDK и любых средств, которые используют ключи учетной записи, если только учетная запись Cosmos DB сначала не будет заблокирована с включенным свойством `disableKeyBasedMetadataWriteAccess`. Дополнительные сведения о том, как включить это свойство, см. в разделе [Предотвращение изменений в пакетах SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Создание блокировки. |
| [az lock list](/cli/azure/lock#az-lock-list) | Вывод сведений о блокировке. |
| [az lock show](/cli/azure/lock#az-lock-show) | Отображение свойств блокировки. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Удаление блокировки. |

## <a name="next-steps"></a>Дальнейшие действия

-[Блокировка ресурсов для предотвращения непредвиденных изменений](../../../../azure-resource-manager/management/lock-resources.md)

-[Документация по Azure Cosmos DB CLI](/cli/azure/cosmosdb).

-[Репозиторий GitHub для Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
