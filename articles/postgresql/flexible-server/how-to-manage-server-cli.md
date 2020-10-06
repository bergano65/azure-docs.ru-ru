---
title: Управление сервером Azure CLI. база данных Azure для PostgreSQL — гибкий сервер
description: Узнайте, как управлять базой данных Azure для PostgreSQL-гибкого сервера из Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0f3c21d5c7f328ddef000ca7f1eaa9d5e18e6ca9
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761895"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Управление базой данных Azure для PostgreSQL-гибкого сервера с помощью Azure CLI

> [!IMPORTANT]
> Гибкий сервер Базы данных Azure для PostgreSQL предоставляется в режиме предварительной версии

В этой статье показано, как управлять гибким сервером, развернутым в Azure. Задачи управления включают в себя вычисление и масштабирование хранилища, сброс пароля администратора и Просмотр сведений о сервере.

## <a name="prerequisites"></a>Предварительные требования
Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу. Для этой статьи требуется запустить локально Azure CLI версии 2.0 или более поздней. Чтобы узнать, какая установлена версия, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Вам потребуется выполнить вход в учетную запись с помощью команды [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login). Обратите внимание на свойство **идентификатора**, которое ссылается на **идентификатор подписки** вашей учетной записи Azure.

```azurecli-interactive
az login
```

Выберите конкретную подписку вашей учетной записи, выполнив команду [az account set](/cli/azure/account). Запишите значение **идентификатора** из выходных данных команды **az login**, чтобы использовать его в команде в качестве значения аргумента **подписки**. Если вы используете несколько подписок, выберите соответствующую, в которой за ресурс будет взиматься плата. Чтобы отобразить все ваши подписки, выполните команду [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Если вы еще не создали гибкий сервер, создайте его, чтобы приступить к работе с этим руководством.

## <a name="scale-compute-and-storage"></a>Масштабирование вычислений и хранилища

Вы можете легко масштабировать свой уровень вычислений, виртуальных ядер и хранилище с помощью следующей команды. Вы можете просмотреть все операции сервера, которые можно выполнить с помощью команды [AZ postgres гибкого сервера Server Overview](https://azure.microsoft.com/services/postgresql/) .

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Ниже приведены подробные сведения об аргументах, приведенных выше:

**Параметр** | **Пример значения** | **Описание**
---|---|---
name | mydemoserver | Введите уникальное имя для сервера. Имя сервера может содержать только строчные буквы, цифры и знак дефиса (-). Его длина должна составлять от 3 до 63 символов.
resource-group | myresourcegroup | Укажите имя группы ресурсов Azure.
sku-name|Standard_D4ds_v3|Введите имя уровня и размера вычислений. Соответствует соглашению Standard_ {размер виртуальной машины} в сокращенном виде. Дополнительные сведения см. на странице с [ценовыми категориями](../concepts-pricing-tiers.md).
storage-size | 6144 | Объем хранилища сервера (в мегабайтах). Минимум 5120 и увеличивается в 1024.

> [!IMPORTANT]
> Невозможно масштабировать хранилище. 

## <a name="manage-postgresql-databases-on-a-server"></a>Управление базами данных PostgreSQL на сервере

К базе данных Azure для сервера PostgreSQL можно подключиться с помощью нескольких приложений. Если на клиентском компьютере установлено PostgreSQL, вы можете использовать локальный экземпляр [psql](https://www.postgresql.org/docs/current/static/app-psql.html), чтобы подключиться к серверу Azure PostgreSQL. Теперь подключимся к серверу Azure PostgreSQL с помощью служебной программы командной строки psql.

1. Чтобы подключиться к серверу базы данных Azure для PostgreSQL, выполните следующую команду psql:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Например, следующая команда устанавливает подключение к базе данных по умолчанию **postgres** на сервере PostgreSQL **mydemoserver.postgres.database.azure.com**, используя учетные данные для доступа. Введите `<server_admin_password>`, указанный при появлении запроса на ввод пароля.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   После подключения служебная программа psql отображает запрос postgres в поле, где вы вводите команды sql. Возможно, в первоначальных выходных данных подключения отобразится предупреждение, так как используемая версия psql может отличаться от версии сервера службы "База данных Azure для PostgreSQL".

   Пример выходных данных psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Если в брандмауэре не настроено разрешение IP-адресов клиента, возникает следующая ошибка:
   >
   > "psql: FATAL: no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: необходимо SSL-подключение". Укажите параметры SSL и повторите попытку.
   >
   > Убедитесь, что IP-адрес клиента разрешен на предыдущем шаге правил брандмауэра.

2. Создайте пустую базу данных с именем "постгресдб" в командной строке, введя следующую команду:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. В командной строке выполните следующую команду, чтобы переключить подключения к вновь созданной базе данных **постгресдб**:

    ```bash
    \c postgresdb
    ```

4. Введите `\q` и нажмите клавишу ВВОД, чтобы выйти из psql.

Вы подключились к серверу службы "База данных Azure для PostgreSQL" через psql и создали пустую пользовательскую базу данных.

## <a name="reset-admin-password"></a>Сброс пароля администратора
Вы можете изменить пароль роли администратора с помощью этой команды.
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Убедитесь, что пароль имеет длину не менее 8 символов и не более 128 символов.
> Пароль должен содержать символы трех из следующих категорий: прописные буквы английского алфавита, строчные буквы английского алфавита, цифры и символы, отличные от буквенно-цифровых.

## <a name="delete-a-server"></a>Удаление сервера

Если вы хотите удалить гибкий сервер PostgreSQL, можно выполнить команду [AZ postgres гибкий-Server DELETE](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Дальнейшие действия

- [Основные сведения о резервном копировании и восстановлении](concepts-backup-restore.md)
- [Настройка и мониторинг сервера](concepts-monitoring.md)
