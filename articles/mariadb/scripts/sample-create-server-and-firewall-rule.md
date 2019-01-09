---
title: Скрипт Azure CLI. Создание Базы данных Azure для MariaDB
description: Этот пример скрипта CLI создает сервер Базы данных Azure для MariaDB и настраивает правило брандмауэра на уровне сервера.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: a7e8a4c8f8572cfdd236b1752e89b3a1b4e7b758
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546816"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Создание сервера MariaDB и настройка правила брандмауэра с помощью Azure CLI
Этот пример скрипта CLI создает сервер Базы данных Azure для MariaDB и настраивает правило брандмауэра на уровне сервера. После успешного выполнения скрипта доступ к серверу MariaDB можно получить из всех служб Azure, а также по настроенному IP-адресу.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении вашей версии Azure CLI см. в разделе [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Создает сервер MariaDB, на котором размещены базы данных. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | Создает правило брандмауэра, чтобы разрешить доступ к серверу и размещенным на нем базам данных по введенному диапазону IP-адресов. |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в: [документации по Azure CLI](/cli/azure).
- Попробуйте использовать дополнительные сценарии: [Примеры Azure CLI для Базы данных Azure для MariaDB](../sample-scripts-azure-cli.md)
