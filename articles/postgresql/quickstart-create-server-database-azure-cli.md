---
title: Краткое руководство. Создание базы данных в службе "База данных Azure для PostgreSQL — отдельный сервер" с помощью Azure CLI
description: Краткое руководство по созданию базы данных в службе "База данных Azure для PostgreSQL — отдельный сервер" с помощью Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030187"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Краткое руководство. Создание базы данных в службе "База данных Azure для PostgreSQL — отдельный сервер" с помощью Azure CLI

> [!TIP]
> Рассмотрите возможность использования более простой команды Azure CLI [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) (сейчас в предварительной версии). Ознакомьтесь с [кратким руководством](./quickstart-create-server-up-azure-cli.md).

В этом кратком руководстве описывается, как с помощью команд [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) в [Azure Cloud Shell](https://shell.azure.com) создать сервер Базы данных Azure для PostgreSQL за 5 минут.  Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Предварительные требования
Для этой статьи требуется запустить локально Azure CLI версии 2.0 или более поздней. Чтобы узнать, какая установлена версия, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Вам потребуется выполнить вход в учетную запись с помощью команды [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Обратите внимание на свойство **идентификатора**, которое ссылается на **идентификатор подписки** вашей учетной записи Azure. 

```azurecli-interactive
az login
```

Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account). Запишите значение **идентификатора** из выходных данных команды **az login**, чтобы использовать его в команде в качестве значения аргумента **подписки**. Если вы используете несколько подписок, выберите соответствующую, в которой за ресурс будет взиматься плата. Чтобы отобразить все ваши подписки, выполните команду [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера Базы данных Azure для PostgreSQL

Создайте [группу ресурсов Azure](../azure-resource-manager/management/overview.md) с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Затем создайте сервер PostgreSQL в этой группе ресурсов. Необходимо указать уникальное имя. В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении именем `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Создайте [сервер базы данных Azure для PostgreSQL](overview.md), выполнив команду [az postgres server create](/cli/azure/postgres/server). Сервер может управлять несколькими базами данных.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Здесь приведены подробные сведения о приведенных выше аргументах. 

**Параметр** | **Пример значения** | **Описание**
---|---|---
name | mydemoserver | Выберите уникальное имя, идентифицирующее базу данных Azure для сервера PostgreSQL. Имя сервера может содержать только строчные буквы, цифры и знак дефиса (-). Его длина должна составлять от 3 до 63 символов.
resource-group | myresourcegroup | Укажите имя группы ресурсов Azure.
location | westus | Расположение сервера в Azure.
admin-user | myadmin | Имя для входа администратора. Не может иметь значение **azure_superuser**, **admin**, **administrator**, **root**, **guest** или **public**.
admin-password | *Надежный пароль* | Пароль администратора. Пароль должен содержать от 8 до 128 символов. Пароль должен содержать символы из таких трех категорий: прописные латинские буквы, строчные латинские буквы, цифры и небуквенно-цифровые знаки.
sku-name|GP_Gen5_2|Введите имя ценовой категории и конфигурацию вычислительных ресурсов. В сокращенной записи соответствует схеме {ценовая категория} _{поколение вычислительных ресурсов}_ {число виртуальных ядер}. Дополнительные сведения см. на странице [цен на Базу данных Azure для PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Версия PostgreSQL на вашем сервере по умолчанию — 9.6. Ознакомьтесь со всеми версиями, поддерживаемыми [здесь](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Все аргументы команды **az postgres server create** можно просмотреть в этом [справочном документе](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- Протокол SSL включен на сервере по умолчанию. Дополнительные сведения по SSL см. в статье [Настройка SSL-подключения](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера 
По умолчанию созданный сервер защищен правилами брандмауэра и не является общедоступным. Вы можете настроить правило брандмауэра на вашем сервере с помощью команды [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule), чтобы предоставить вашей локальной среде доступ для подключения к серверу. 

В приведенном ниже примере создается правило брандмауэра с именем `AllowMyIP`, которое разрешает подключения с определенного IP-адреса — 192.168.0.1. Замените IP-адрес или диапазон IP-адресов, которые соответствуют расположению, из которого будет устанавливаться подключение.  Если не знаете как найти свой IP-адрес, перейдите на страницу [https://whatismyipaddress.com/](https://whatismyipaddress.com/) для получения IP-адреса.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Убедитесь, что брандмауэр сети разрешает доступ к порту 5432, используемый серверами Базы данных Azure для PostgreSQL, чтобы избежать проблем с подключением. 

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Результаты выводятся в формате JSON. Запишите **administratorLogin** и **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Подключение к Базе данных Azure для PostgreSQL с помощью psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) — это популярный клиент, используемый для подключения к серверам PostgreSQL. Вы можете подключиться к серверу, используя параметр **psql** в [Azure Cloud Shell](../cloud-shell/overview.md). Кроме того, можно использовать psql в локальной среде, если она доступна. Пустая база данных postgres уже создана вместе с новым сервером PostgreSQL, который можно использовать для подключения к PSQL, как показано ниже. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Если вы предпочитаете использовать URL-путь для подключения к Postgres, закодируйте с помощью URL-адреса знак @ в имени пользователя с использованием `%40`. Например, строка подключения для psql будет выглядеть так:
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Очистка ресурсов
Если эти ресурсы не требуются для изучения другого руководства, вы можете их удалить. Для этого выполните следующую команду: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Если вы хотите удалить созданный сервер, выполните команду [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)
> 
> [Развертывание веб-приложения Django с помощью PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Подключение с помощью приложения Node.JS](./connect-nodejs.md)

