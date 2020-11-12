---
title: Скрипты Azure CLI для операций с пропускной способностью (ЕЗ/с) с использованием ресурсов API службы Azure Cosmos DB Core (SQL)
description: Скрипты Azure CLI для операций с пропускной способностью (ЕЗ/с) с использованием ресурсов API службы Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: e48a167327a83483b44d88fc73a0254df6f85c55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087355"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Выполнение операций с пропускной способностью (ЕЗ/с) через Azure CLI для базы данных или контейнера с использованием API Azure Cosmos DB Core (SQL)
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.12.1 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает базу данных API Core (SQL) с общей пропускной способностью и контейнер API Core (SQL) с выделенной пропускной способностью, а затем обновляет данные пропускной способности как для базы данных, так и для контейнера. Затем этот скрипт переключает режим пропускной способности со стандартного на автомасштабирование и считывает значение пропускной способности, установленное после переноса.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Создает учетную запись Azure Cosmos DB. |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Создает базу данных Azure Cosmos Core (SQL). |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Создает контейнер Azure Cosmos Core (SQL). |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Обновляет данные пропускной способности для базы данных Azure Cosmos Core (SQL). |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Обновляет данные пропускной способности для контейнеров Azure Cosmos Core (SQL). |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Переходит с одной пропускной способности на другую для базы данных Azure Cosmos Core (SQL). |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Переходит с одной пропускной способности на другую для контейнера Azure Cosmos Core (SQL). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure Cosmos DB CLI см. в [этой документации](/cli/azure/cosmosdb).

Все примеры сценариев Azure Cosmos DB CLI можно найти в [этом репозитории на сайте GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
