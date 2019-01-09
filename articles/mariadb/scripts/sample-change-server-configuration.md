---
title: Скрипт Azure CLI. Изменение конфигураций серверов
description: В этом примере скрипта CLI перечисляются все доступные конфигурации серверов и обновляется значение innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 514f2fe7e8c2b94e175bc6f6fb0cec8ea3d0c9c6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547428"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Получение списка и обновление конфигураций сервера Базы данных Azure для MariaDB с помощью Azure CLI
В этом примере скрипта CLI выводится список всех доступных параметров конфигурации и их допустимые значения для сервера Базы данных Azure для MariaDB. Кроме того, здесь задается значение *innodb_lock_wait_timeout*, отличное от значения по умолчанию.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Создает сервер MariaDB, на котором размещены базы данных. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Позволяет получить список конфигураций сервера Базы данных Azure для MariaDB. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Обновляет конфигурацию сервера Базы данных Azure для MariaDB. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Обновляет конфигурацию сервера Базы данных Azure для MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в: [документации по Azure CLI](/cli/azure).
- Попробуйте использовать дополнительные сценарии: [Примеры Azure CLI для Базы данных Azure для MariaDB](../sample-scripts-azure-cli.md)
- Дополнительные сведения о параметрах сервера см. в статье [Как настроить параметры сервера в базе данных Azure для MariaDB с помощью портала Azure](../howto-server-parameters.md).
