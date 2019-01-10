---
title: Краткое руководство. Создание Базы данных Azure для сервера MariaDB — Azure CLI
description: В этом кратком руководстве описывается создание сервера Базы данных Azure для MariaDB в группе ресурсов Azure с помощью Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 19c1952a25e6a6db9b81b74f79bdcd2970cb2c78
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538755"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Создание сервера Базы данных Azure для MariaDB с помощью Azure CLI

Azure CLI можно использовать для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом кратком руководстве описывается создание сервера Базы данных Azure для MariaDB в группе ресурсов Azure с помощью Azure CLI за 5 минут. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы устанавливаете и используете CLI локально, для этого краткого руководства вам потребуется Azure CLI версии 2.0 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если у вас несколько подписок, выберите подписку, содержащую ресурс, или подписку, на которую выставляются счета. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#az-account-set).

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Создание сервера Базы данных Azure для MariaDB

Создайте сервер Базы данных Azure для MariaDB, выполнив команду [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). Сервер может управлять несколькими базами данных. Как правило, для каждого проекта и для каждого пользователя используется отдельная база данных.

Параметр | Образец значения | ОПИСАНИЕ
---|---|---
name | **mydemoserver** | Введите уникальное имя, идентифицирующее сервер Базы данных Azure для MariaDB. Имя сервера может содержать только строчные буквы, цифры и знак дефиса (-). Оно должно содержать от 3 до 63 символов.
resource-group | **myresourcegroup** | Введите имя группы ресурсов Azure.
sku-name | **GP_Gen5_2** | Имя номера SKU. Сокращенная запись соответствует соглашению: *ценовая категория*\_*поколение вычислительных ресурсов*\_*число виртуальных ядер*. Под этой таблицей приведены дополнительные сведения о параметре **sku-name**.
backup-retention | **7** | Срок хранения резервной копии. Указывается в днях. Диапазон: 7–35 
geo-redundant-backup | **Disabled** | Позволяет включить или отключить создание геоизбыточных резервных копий для этого сервера. Допустимые значения: **Enabled**, **Disabled**.
location | **westus** | Расположение сервера в Azure.
ssl-enforcement | **Включено** | Позволяет включить или отключить SSL для этого сервера. Допустимые значения: **Enabled**, **Disabled**.
storage-size | **51200** | Объем хранилища сервера (в мегабайтах). Допустимые размеры хранилища: 5120 МБ (минимум) с шагом увеличения в 1024 МБ. Ознакомьтесь с документом о [ценовых категориях](./concepts-pricing-tiers.md), чтобы узнать больше об ограничениях размера хранилища. 
версия | **10.2** | Основной номер версии ядра СУБД MariaDB.
admin-user | **myadmin** | Имя для входа администратора. Не используйте для параметра **admin-user** такие значения: **azure_superuser**, **admin**, **administrator**, **root**, **guest** или **public**.
admin-password | *ваш пароль* | Пароль администратора. Ваш пароль должен состоять из 8–128 символов. Пароль должен содержать знаки из таких трех категорий: прописные латинские буквы, строчные латинские буквы, цифры и небуквенно-цифровые знаки.

Значение параметра **sku-name** соответствует соглашению *ценовая категория*\_*поколение вычислительных ресурсов*\_*количество виртуальных ядер*, как показано в примерах ниже:
+ `--sku-name B_Gen5_4` соответствует ценовой категории "Базовый", вычислительным ресурсам 5-го поколения и 4 виртуальным ядрам.
+ `--sku-name GP_Gen5_32` соответствует ценовой категории "Общего назначения", вычислительным ресурсам 5-го поколения и 32 виртуальным ядрам.
+ `--sku-name MO_Gen5_2` соответствует ценовой категории "Оптимизировано для памяти", вычислительным ресурсам 5-го поколения и 2 виртуальным ядрам.

Сведения о допустимых значениях по регионам и ценовым категориям см. на странице [ценовых категорий](./concepts-pricing-tiers.md).

В следующем примере создается сервер с именем **mydemoserver** в регионе "Западная часть США". Сервер находится в группе ресурсов **myresourcegroup** и имеет имя для входа администратора сервера **myadmin**. Это сервер 5-го поколения с ценовой категорией "Общего назначения" и 2 виртуальными ядрами. Имя сервера сопоставляется с DNS-именем и должно быть глобально уникальным в Azure. Замените `<server_admin_password>` паролем администратора сервера.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-a-firewall-rule"></a>Настройка правила брандмауэра

Создайте правило брандмауэра на уровне сервера Базы данных Azure для MariaDB, выполнив команду [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как программа командной строки mysql или MySQL Workbench, подключаться к серверу через брандмауэр службы "База данных Azure для MariaDB". 

В приведенном ниже примере создается правило брандмауэра с именем `AllowMyIP`, которое разрешает подключения с определенного IP-адреса — 192.168.0.1. Замените IP-адрес или диапазон IP-адресов в соответствии с расположением, из которого вы подключаетесь. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Подключитесь к Базе данных Azure для MariaDB через порт 3306. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 3306 может быть запрещен. В таком случае вы не сможете подключиться к серверу. Для этого ваш ИТ-отдел должен открыть порт 3306.
> 

## <a name="configure-ssl-settings"></a>Настройка параметров SSL

По умолчанию между сервером и клиентскими приложениями применяется SSL-соединение. Эта настройка по умолчанию гарантирует безопасное перемещение данных за счет шифрования потока данных через Интернет. В этом руководстве отключите SSL-соединения для вашего сервера. Мы не рекомендуем так делать для рабочих серверов. Дополнительные сведения см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MariaDB](./howto-configure-ssl.md).

Следующий пример отключает применение SSL на сервере Базы данных Azure для MariaDB:
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа. Чтобы получить информацию о подключении, выполните следующую команду:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Подключение к серверу с помощью программы командной строки mysql

Подключитесь к серверу с помощью программы командной строки mysql. Эту программу командной строки можно скачать [отсюда](https://dev.mysql.com/downloads/) и установить на компьютер. Программу командной строки также можно открыть, нажав кнопку **Попробовать** в примере кода в этой статье. Другой способ доступа к программе командной строки — нажать кнопку **>_** на верхней правой панели инструментов на портале Azure, чтобы открыть **Azure Cloud Shell**.

Чтобы подключиться к серверу с помощью программы командной строки mysql:

1. Подключитесь к серверу:

  ```azurecli-interactive
  mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  ```

2. Просмотрите состояние сервера в командной строке `mysql>`.

  ```sql
  status
  ```
  У вас должен отобразиться примерно такой текст.

  ```bash
  C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  Enter password: ***********
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 65512
  Server version: 5.6.39.0 MariaDB Server

  Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

  mysql> status
  --------------
  mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

  Connection id:          64681
  Current database:
  Current user:           myadmin@40.118.201.21
  SSL:                    Cipher in use is AES256-SHA
  Current pager:          stdout
  Using outfile:          ''
  Using delimiter:        ;
  Server version:         5.6.39.0 MariaDB Server
  Protocol version:       10
  Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
  Server characterset:    latin1
  Db     characterset:    latin1
  Client characterset:    utf8
  Conn.  characterset:    utf8
  TCP port:               3306
  Uptime:                 1 day 3 hours 28 min 50 sec

  Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
  --------------

  mysql>
  ```

> [!TIP]
> Дополнительные команды см. в [разделе 4.5.1 справочного руководства по MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Подключение к серверу с помощью MySQL Workbench

1.  Откройте MySQL Workbench на клиентском компьютере. Если это приложение еще не установлено, [скачайте](https://dev.mysql.com/downloads/workbench/) и установите его.

2.  В диалоговом окне **Настройка нового подключения** на вкладке **Параметры** введите следующие сведения:

 ![Настройка нового подключения](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

  | Параметр | Рекомендуемое значение | ОПИСАНИЕ |
  |---|---|---|
  | Имя подключения | **Подключение demo** | Введите метку для этого подключения (имя подключения может быть любым) |
  | Способ подключения | **Standard (TCP/IP)** (Стандартный способ (по протоколу TCP/IP)) | Используйте протокол TCP/IP для подключения к Базе данных Azure для MariaDB. |
  | имя узла; | **mydemoserver.mariadb.database.azure.com** | Имя сервера, которое вы записали ранее. |
  | Порт | **3306** | Порт по умолчанию Базы данных Azure для MariaDB. |
  | Имя пользователя | **myadmin@mydemoserver** | Имя для входа администратора сервера, которое вы указали ранее. |
  | Пароль | *ваш пароль* | Используйте пароль учетной записи администратора, настроенный ранее. |

3. Щелкните **Проверить подключение**, чтобы проверить, все ли параметры настроены правильно.

4. Выберите только что созданное подключение, чтобы успешно подключиться к серверу.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если использованные здесь ресурсы не требуются для изучения другого руководства, вы можете их удалить. Для этого выполните следующую команду: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Если вы хотите удалить только тот сервер, который вы создали в этом руководстве, запустите команду [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Проектирование базы данных MariaDB с помощью Azure CLI](./tutorial-design-database-cli.md)
