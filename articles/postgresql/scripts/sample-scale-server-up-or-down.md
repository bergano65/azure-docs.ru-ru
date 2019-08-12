---
title: Скрипт Azure CLI. Масштабирование и мониторинг Базы данных Azure для PostgreSQL
description: Здесь приведен пример скрипта Azure CLI, который масштабирует сервер базы данных Azure для PostgreSQL до нужного уровня производительности после выполнения запроса к метрикам.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 08/01/2019
ms.openlocfilehash: 6e1b6e5b09a3b9f3da5760fc50c531ee524dc8d4
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728773"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Мониторинг и масштабирование отдельного сервера PostgreSQL с помощью Azure CLI
Этот пример скрипта CLI масштабирует вычислительные ресурсы и хранилище для отдельного сервера Базы данных Azure для PostgreSQL после выполнения запроса к метрикам. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные. Замените идентификатор подписки, используемый в командах `az monitor`, собственным.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Обновляет свойства сервера PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Выводит список значений метрики для ресурсов. |
| [az group delete](/cli/azure/group) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- См. о [вычислительных ресурсах и хранилище Базы данных Azure для PostgreSQL](../concepts-pricing-tiers.md).
- Дополнительные скрипты — [Примеры Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md)
- См. об [Azure CLI](/cli/azure).
