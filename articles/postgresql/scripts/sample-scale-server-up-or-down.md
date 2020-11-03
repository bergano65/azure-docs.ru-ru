---
title: Скрипт Azure CLI. Масштабирование и мониторинг Базы данных Azure для PostgreSQL
description: Здесь приведен пример скрипта Azure CLI, который масштабирует сервер базы данных Azure для PostgreSQL до нужного уровня производительности после выполнения запроса к метрикам.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: a848e14f854385ed1603918ab7e7a274667f2324
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427540"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Мониторинг и масштабирование отдельного сервера PostgreSQL с помощью Azure CLI
Этот пример скрипта CLI масштабирует вычислительные ресурсы и хранилище для отдельного сервера Базы данных Azure для PostgreSQL после выполнения запроса к метрикам. Вычислительные ресурсы можно масштабировать произвольно. Объем хранилища можно только наращивать. 

> [!IMPORTANT] 
> Масштаб хранилища можно только увеличить вертикально, но не уменьшить.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта
Укажите в скрипте идентификатор своей подписки.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Command** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Обновляет свойства сервера PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Выводит список значений метрики для ресурсов. |
| [az group delete](/cli/azure/group) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
- См. о [вычислительных ресурсах и хранилище Базы данных Azure для PostgreSQL](../concepts-pricing-tiers.md).
- Дополнительные скрипты — [Примеры Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md)
- См. об [Azure CLI](/cli/azure).
