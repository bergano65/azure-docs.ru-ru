---
title: Создание блокировки ресурса для базы данных и коллекции для API MongoDB в Azure Cosmos DB
description: Создание блокировки ресурса для базы данных и коллекции для API MongoDB в Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: cfa3bd377376b5b72bd1d0f18a7905196958a17e
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514585"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Создание блокировки ресурса для API Azure Cosmos DB для MongoDB с помощью Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.6.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Блокировки ресурсов не работают для изменений, внесенных пользователями, которые подключаются с помощью любого пакета SDK MongoDB, Mongoshell или портала Azure, если только учетная запись Cosmos DB сначала не будет заблокирована с помощью включения свойства `disableKeyBasedMetadataWriteAccess`. Дополнительные сведения о том, как включить это свойство, см. в разделе [Предотвращение изменений в пакетах SDK](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Создает блокировку. |
| [az lock list](/cli/azure/lock#az-lock-list) | Выводит сведения о блокировке. |
| [az lock show](/cli/azure/lock#az-lock-show) | Отображает свойства блокировки. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Удаляет блокировку. |

## <a name="next-steps"></a>Дальнейшие действия

-[Блокировка ресурсов для предотвращения непредвиденных изменений](../../../../azure-resource-manager/management/lock-resources.md)

-[Документация по Azure Cosmos DB CLI](/cli/azure/cosmosdb).

-[Репозиторий GitHub для Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
