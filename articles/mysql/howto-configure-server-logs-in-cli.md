---
title: Доступ к медленным журналам запросов - Azure CLI - База данных Azure для MyS'L
description: В этой статье описывается, как получить доступ к медленным журналам запросов в базе данных Azure для MyS'L с помощью Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062480"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Настройка и доступ к медленным журналам запросов с помощью Azure CLI
Вы можете загрузить базу данных Azure для журналов медленных запросов MyS'L с помощью Azure CLI, утилиты командной строки Azure.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [База данных Azure для сервера MyS'L](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере.

## <a name="configure-logging"></a>Настройка журнала
Можно настроить на сервере доступ к журналу медленных запросов MySQL, выполнив следующие действия:
1. Включите медленный журнал запроса, установив параметр **медленного\_журнала запроса\_** на ON.
2. Настройте другие параметры, такие как **long\_query\_time** и **log\_slow\_admin\_statements**.

Ознакомьтесь со статьей [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI.

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Получение списка журналов для сервера базы данных Azure для MySQL
Чтобы перечислить доступные файлы медленного журнала запроса для вашего сервера, запустите команду [списка серверов az mysql.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Вы можете вывести список файлов журнала для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**. Затем направьте список файлов журнала в текстовый файл с именем **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Скачивание журналов с сервера
Команда [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) позволяет скачать отдельные файлы журналов для сервера. 

В следующем примере в локальную среду скачивается определенный файл журнала для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте о [медленных журналах запросов в базе данных Azure для MyS'L.](concepts-server-logs.md)
