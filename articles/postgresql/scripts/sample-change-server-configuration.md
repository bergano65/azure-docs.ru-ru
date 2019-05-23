---
title: Скрипт Azure CLI. Изменение конфигураций серверов
description: Этот пример сценария интерфейса командной строки выводит список всех доступных параметров конфигурации сервера и обновляет значение одного из этих параметров.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 6b5a855c8db5cb87f313e14c42396ae70b407e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66122088"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Вывод и обновление конфигураций сервера базы данных Azure для PostgreSQL с помощью Azure CLI
В этом примере сценария интерфейса командной строки выводятся все доступные параметры конфигурации и их допустимые значения для сервера базы данных Azure для PostgreSQL. Кроме того, он задает значение *log_retention_days*, отличное от значения по умолчанию.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Вывод конфигураций сервера базы данных Azure для PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Обновление конфигурации сервера базы данных Azure для PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Отображение конфигурации сервера базы данных Azure для PostgreSQL. |
| [az group delete](/cli/azure/group) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
- Дополнительные скрипты — [Примеры Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md)
- Дополнительные сведения о параметрах сервера см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](../howto-configure-server-parameters-using-portal.md).
