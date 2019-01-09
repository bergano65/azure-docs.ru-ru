---
title: Скрипт Azure CLI. Масштабирование сервера Базы данных Azure для MariaDB
description: Этот пример скрипта CLI масштабирует сервер Базы данных Azure для MariaDB до нужного уровня производительности после выполнения запроса к метрикам.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: ef01c38dddee0a74477ef331f935bda2787866df
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546375"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Мониторинг и масштабирование сервера Базы данных Azure для MariaDB с помощью Azure CLI
Этот пример скрипта CLI масштабирует отдельный сервер Базы данных Azure для MariaDB до нужного уровня производительности после выполнения запроса к метрикам.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные. Замените идентификатор подписки, используемый в командах `az monitor`, собственным.[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Создает сервер MariaDB, на котором размещены базы данных. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Выводит список значений метрики для ресурсов. |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в: [документации по Azure CLI](/cli/azure).
- Попробуйте использовать дополнительные сценарии: [Примеры Azure CLI для Базы данных Azure для MariaDB](../sample-scripts-azure-cli.md)
- Дополнительные сведения о масштабировании см. в статье о [ценовых категориях] (../concepts-pricing-tiers.md.
