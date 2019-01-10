---
title: Скрипт Azure CLI. Скачивание журналов сервера в Базе данных Azure для PostgreSQL
description: Этот пример сценария Azure CLI демонстрирует, как включить и скачать журналы сервера базы данных Azure для PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: cd4346c738e1c1c52ee181f764f827b1b1060650
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547207"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Включение и загрузка журналов медленных запросов для сервера базы данных Azure для PostgreSQL с помощью Azure CLI
Этот пример сценария интерфейса командной строки позволяет скачать журналы медленных запросов на одном сервере базы данных Azure для PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные. Замените &lt;log_file_name&gt; в командах `az monitor` выбранным именем для файла журнала сервера.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Перечисление значений конфигурации для сервера. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Обновление конфигурации сервера. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Выводит список файлов журнала для сервера. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Скачивание файлов журнала. |
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
- Дополнительные скрипты — [Примеры Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md)
- [Настройка журналов сервера и получение к ним доступа с помощью портала Azure](../howto-configure-server-logs-in-portal.md)
