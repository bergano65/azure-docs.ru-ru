---
title: Настройка параметров сервера - Azure CLI - База данных Azure для MyS'L
description: В этой статье описывается настройка параметров службы в базе данных Azure для MySQL с помощью служебной программы командной строки CLI Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f3027909d1c4684e2ef5d1b6e967cb11f570fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062432"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Настройка параметров сервера с помощью Azure CLI
С помощью служебной программы командной строки (Azure CLI) можно вывести список параметров конфигурации для сервера базы данных Azure для MySQL, а также отобразить и обновить их. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [База данных Azure для сервера MyS'L](quickstart-create-mysql-server-database-using-azure-cli.md)
- Программа командной строки [Azure CLI](/cli/azure/install-azure-cli) (или используйте Azure Cloud Shell в браузере).

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Получение списка параметров конфигурации сервера для базы данных Azure для сервера MySQL
Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните команду [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list).

Например, можно вывести список параметров конфигурации сервера для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Определение каждого из перечисленных параметров см. в разделе ссылок MySQL на странице[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Системные переменные сервера).

## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера
Чтобы отобразить сведения об определенном параметре конфигурации для сервера, выполните команду [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show).

Этот пример отображает сведения параметра конфигурации сервера **slow\_query\_log** для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера
Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра СУБД сервера MySQL. Чтобы обновить конфигурацию, выполните команду [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set). 

Чтобы обновить параметры конфигурации **slow\_query\_log** для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup**, используйте следующую команду:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Если вы хотите сбросить значение параметра конфигурации, нужно просто опустить необязательный параметр `--value`, и служба применит значение по умолчанию. В приведенном выше примере это может выглядеть следующим образом.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Этот код выполняет сброс конфигурации **slow\_query\_log** к значению по умолчанию **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Работа с параметром часового пояса

### <a name="populating-the-time-zone-tables"></a>Заполнение таблиц часовых поясов

Таблицы часовых поясов на сервере можно заполнить, вызвав хранимую процедуру `az_load_timezone` с помощью такого инструмента, как командная строка MySQL или MySQL Workbench.

> [!NOTE]
> Если вы используете команду `az_load_timezone` в MySQL Workbench, может потребоваться предварительно отключить режим безопасного обновления с помощью `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Необходимо перезапустить сервер, чтобы обеспечить правильное заселение столиков часового пояса. Для перезагрузки сервера используйте [портал Azure](howto-restart-server-portal.md) или [CLI.](howto-restart-server-cli.md)

Чтобы просмотреть доступные значения часового пояса, выполните следующую команду.

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Установка часового пояса глобального уровня

Часовой пояс глобального уровня можно задать с помощью команды [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set).

Чтобы обновить параметр конфигурации **time\_zone** для сервера **mydemoserver.mysql.database.azure.com** в группе ресурсов **myresourcegroup** и задать для него значение **US/Pacific**, используйте следующую команду.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Настройка часового пояса уровня сеанса

Часовой пояс уровня сеанса можно задать, выполнив команду `SET time_zone` в командной строке MySQL или MySQL Workbench. В приведенном ниже примере задается часовой пояс **US/Pacific** (США, Тихоокеанский регион).  

```sql
SET time_zone = 'US/Pacific';
```

Описание [функций даты и времени](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz) можно прочитать в документации по MySQL.


## <a name="next-steps"></a>Дальнейшие действия

- Настройка [параметров сервера на портале Azure](howto-server-parameters.md)