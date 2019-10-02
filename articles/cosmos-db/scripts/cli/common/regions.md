---
title: Добавление регионов, изменение приоритета для отработки отказа, запуск отработки отказа для учетной записи Azure Cosmos
description: Добавление регионов, изменение приоритета для отработки отказа, запуск отработки отказа для учетной записи Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275111"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Добавление регионов, изменение приоритета для отработки отказа, запуск отработки отказа для учетной записи Azure Cosmos с помощью Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.73 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

В этом сценарии описаны три операции:

- добавление региона в имеющуюся учетную запись Azure Cosmos;
- изменение приоритета для отработки отказа (применяется к учетным записям, в которых используется автоматический переход на другой ресурс);
- активация перехода на другой ресурс вручную из основных в дополнительные регионы (применяется к учетным записям с переходом на другой ресурс вручную).

> [!NOTE]
> Операции добавления и удаления регионов в учетной записи Cosmos невозможно выполнить при изменении других свойств.

> [!NOTE]
> В этом примере демонстрируется использование учетной записи API SQL (Core), но эти операции идентичны во всех API-интерфейсах базы данных в Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Создает учетную запись базы данных Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Обновляет учетную запись Azure Cosmos DB (добавление и удаление региона). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Обновляет приоритет для отработки отказа или запускает отработку отказа для учетной записи Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure Cosmos DB CLI см. в [этой документации](/cli/azure/cosmosdb).

Все примеры сценариев Azure Cosmos DB CLI можно найти в [этом репозитории на сайте GitHub](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
