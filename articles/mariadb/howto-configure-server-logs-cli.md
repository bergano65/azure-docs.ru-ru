---
title: Доступ к журналам запросов с высокой производительностью. Azure CLI — база данных Azure для MariaDB
description: В этой статье описывается, как получить доступ к журналам ожидания в базе данных Azure для MariaDB с помощью служебной программы командной строки Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767387"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Настройка и доступ к журналам запросов с высокой занесением с помощью Azure CLI
Вы можете скачать базу данных Azure для MariaDBных журналов запросов с помощью Azure CLI, служебной программы командной строки Azure.

## <a name="prerequisites"></a>Технические условия
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере.

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Настройка ведения журнала для Базы данных Azure для MariaDB
Можно настроить сервер для доступа к журналу медленных запросов MariaDB, выполнив следующие действия:
1. Включите ведение журнала, установив для параметра **slow\_query\_log** значение ON.
2. Настройте другие параметры, такие как **long\_query\_time** и **log\_slow\_admin\_statements**.

Ознакомьтесь со статьей [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI.

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Получение списка журналов сервера в Базе данных Azure для MariaDB
Чтобы получить список доступных файлов журнала запросов с высокой запятыми для сервера, выполните команду [AZ MariaDB Server-Logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

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

## <a name="next-steps"></a>Дальнейшие действия
- Сведения о [очень низких журналах запросов в базе данных Azure для MariaDB](concepts-server-logs.md).
