---
title: Доступ к медленным журналам запросов - Azure CLI - База данных Azure для MariaDB
description: В этой статье описывается, как получить доступ к медленным журналам в базе данных Azure для MariaDB с помощью утилиты командной строки Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270542"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Настройка и доступ к медленным журналам запросов с помощью Azure CLI
Вы можете загрузить базу данных Azure для журналов медленных запросов MariaDB с помощью Azure CLI, утилиты командной строки Azure.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере.

## <a name="configure-logging"></a>Настройка журнала
Можно настроить на сервере доступ к журналу медленных запросов MySQL, выполнив следующие действия:
1. Включите медленный журнал запроса, установив параметр **медленного\_журнала запроса\_** на ON.
2. Выберите, где вывести журналы на использование **вывода журнала.\_** Для отправки журналов в локальные системы хранения данных и в диагностические журналы Azure Monitor выберите **файл.** Чтобы отправлять журналы только в журналы мониторинга Azure, выберите **Нет**
3. Настройте другие параметры, такие как **long\_query\_time** и **log\_slow\_admin\_statements**.

Ознакомьтесь со статьей [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-cli.md), чтобы узнать, как задать значение этих параметров с помощью Azure CLI.

Например, приведенная ниже команда интерфейса командной строки включает журнал медленных запросов, задает длительность запроса в 10 секунд и отключает ведение журнала медленных инструкций администрирования. Наконец, она выводит параметры конфигурации, чтобы вы могли их просмотреть.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Получение списка журналов сервера в Базе данных Azure для MariaDB
Если **log_output** настроена на "Файл", вы можете получить доступ к журналам непосредственно из локального хранилища сервера. Чтобы перечислить доступные файлы медленного журнала запроса для вашего сервера, запустите команду [списка серверов-журналов az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Вы можете вывести список файлов журнала для сервера **mydemoserver.mariadb.database.azure.com** в группе ресурсов **myresourcegroup**. Затем направьте список файлов журнала в текстовый файл с именем **log\_files\_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Скачивание журналов с сервера
Если **log_output** настроена на "Файл", вы можете скачать отдельные файлы журнала с вашего сервера с командой [загрузки сервера az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download)

В следующем примере в локальную среду скачивается определенный файл журнала для сервера **mydemoserver.mariadb.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте о [медленных журналах запросов в базе данных Azure для MariaDB.](concepts-server-logs.md)
