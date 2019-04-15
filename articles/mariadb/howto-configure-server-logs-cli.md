---
title: Доступ к журналам сервера в службе "База данных Azure для MariaDB" с помощью Azure CLI
description: В этой статье объясняется, как получить доступ к журналам сервера в службе "База данных Azure для MariaDB" с помощью программы Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: 403f111882b1bd151b26af56fd614355e7ba88e3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539217"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Скачать журналы сервера из службы "База данных Azure для MariaDB" можно с помощью Azure CLI — программы для работы с Azure.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере.

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Настройка ведения журнала для Базы данных Azure для MariaDB
Можно настроить сервер для доступа к журналу медленных запросов MariaDB, выполнив следующие действия:
1. Включите ведение журнала, установив для параметра **slow\_query\_log** значение ON.
2. Настройте другие параметры, такие как **long\_query\_time**  и  **log\_slow\_admin\_statements**.

Ознакомьтесь со статьей [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI.

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Получение списка журналов сервера в Базе данных Azure для MariaDB
Чтобы получить список доступных файлов журналов для сервера, выполните команду [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list).

Вы можете вывести список файлов журнала для сервера **mydemoserver.mariadb.database.azure.com** в группе ресурсов **myresourcegroup**. Затем направьте список файлов журнала в текстовый файл с именем **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Скачивание журналов с сервера
Команда [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) позволяет скачать отдельные файлы журналов для сервера.

В следующем примере в локальную среду скачивается определенный файл журнала для сервера **mydemoserver.mariadb.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь со статьей [Журналы сервера в Базе данных Azure для MariaDB](concepts-server-logs.md).
