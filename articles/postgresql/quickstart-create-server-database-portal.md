---
title: Краткое руководство. Создание сервера — портал Azure — База данных Azure для PostgreSQL — отдельный сервер
description: В этом кратком руководстве описывается создание сервера Базы данных Azure для PostgreSQL и управление им с помощью портала Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 6e43d00722dd86934c8f95e06a3b8b590b263d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91705166"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Краткое руководство. Создание сервера Базы данных Azure для PostgreSQL с помощью портала Azure

База данных Azure для PostgreSQL — это управляемая служба, с помощью которой можно запускать и масштабировать базы данных PostgreSQL высокой доступности, а также управлять ими в облаке. В этом кратком руководстве описывается, как создать один сервер Базы данных Azure для PostgreSQL с помощью портала Azure примерно за 5 минут.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
Откройте веб-браузер и перейдите на [портал](https://portal.azure.com/). Введите свои учетные данные для входа на портал. Панель мониторинга службы является представлением по умолчанию.

## <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера Базы данных Azure для PostgreSQL

Сервер службы "База данных Azure для PostgreSQL" создается с настроенным набором [вычислительных ресурсов и ресурсов хранения](./concepts-pricing-tiers.md). Он создается в [группе ресурсов Azure](../azure-resource-manager/management/overview.md).

Для создания сервера Базы данных Azure для PostgreSQL выполните следующие действия.

1. Выберите **Создать ресурс** в левом верхнем углу страницы портала.

2. Выберите **Базы данных** > **База данных Azure для PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;.":::

3. Выберите вариант развертывания **Отдельный сервер**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;.":::

4. Заполните форму **Основные данные** , указав следующую информацию:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;." или "Оптимизированная для операций в памяти". 
   
5. Выберите **Просмотр и создание** , чтобы просмотреть выбранные элементы. Щелкните **Создать**, чтобы подготовить сервер. Эта операция может занять несколько минут.

6. На панели инструментов щелкните значок **Уведомления** (колокольчик), чтобы отслеживать процесс развертывания. Когда развертывание завершится, выберите действие **Перейти к ресурсу**, которое открывает страницу **Обзор** для сервера.

Создается пустая база данных **postgres**. Кроме того, будет создана база данных **azure_maintenance**, которая используется для отделения процессов управляемой службы от действий пользователя. Не удается получить доступ к базе данных **azure_maintenance**.

## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера
По умолчанию создаваемый сервер не является общедоступным. Поэтому для доступа к IP-адресу необходимо предоставить разрешения. Перейдите к ресурсу сервера на портале Azure и выберите **Безопасность подключения** в левой части меню для этого ресурса сервера. Если вы не знаете, как найти ресурс, ознакомьтесь с разделом [Открытие ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;.":::
  
Выберите **Добавить текущий IP-адрес клиента** и щелкните **Сохранить**. Вы можете добавить дополнительные IP-адреса или указать диапазон адресов, чтобы подключаться к серверу с любого из этих IP-адресов. Дополнительные сведения см. в статье [Правила брандмауэра в Базе данных Azure для PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Чтобы избежать проблем с подключением, проверьте, разрешена ли в вашей сети передача исходящего трафика через порт 5432. База данных Azure для PostgreSQL использует этот порт.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Подключение к Базе данных Azure для PostgreSQL с помощью psql

Вы можете использовать популярные клиенты для PostgreSQL: [psql](http://postgresguide.com/utilities/psql.html) или [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html). В этом кратком руководстве предполагается, что для подключения используется psql в [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) на портале Azure.

1. Запишите имя нового сервера, имя администратора этого сервера, пароль и идентификатор подписки из раздела **Обзор**.

2. Откройте Azure Cloud Shell на портале, щелкнув значок в верхней левой части.

   > [!NOTE]
   > Если вы открываете Cloud Shell первый раз, отобразится запрос на создание группы ресурсов и учетной записи хранения. Это одноразовое действие, которое автоматически применяется для всех сеансов. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;.":::

3. Воспользуйтесь следующей командой в терминале Azure Cloud Shell. Замените предложенные значения реальными именами сервера и администратора. Используйте пустую базу данных **postgres** от имени администратора в следующем формате: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Ниже показано, как выглядит этот процесс в терминале Cloud Shell.
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. В том же терминале Azure Cloud Shell создайте базу данных с именем **guest**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Переключите текущее подключение на новую базу данных **guest**.

   ```bash
   \c guest
   ```
6. Введите `\q` и нажмите клавишу ВВОД, чтобы закрыть psql. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы успешно создали сервер Базы данных Azure для PostgreSQL в группе ресурсов. Если вы ожидаете, что в будущем эти ресурсы вам не понадобятся, их можно удалить, удалив группу ресурсов или сам сервер PostgreSQL. 

Чтобы удалить группу ресурсов, сделайте следующее:

1. Войдите на портал Azure; найдите в поиске и выберите **Группы ресурсов**. 
2. В списке групп ресурсов выберите имя нужной группы ресурсов.
3. На странице **Обзор** для группы ресурсов выберите **Удалить группу ресурсов**.
4. В диалоговом окне подтверждения введите имя группы ресурсов и щелкните **Удалить**.

Чтобы удалить сервер, нажмите кнопку **Удалить** на его странице **Обзор**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Снимок экрана, на котором показан пункт меню &quot;База данных Azure для PostgreSQL&quot;.":::

## <a name="next-steps"></a>Дальнейшие шаги
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)
