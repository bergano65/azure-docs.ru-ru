---
title: Краткое руководство. Создание сервера Базы данных Azure для MySQL с помощью портала Azure
description: В этой статье описывается быстрое (в пределах пяти минут) создание примера базы данных Azure для сервера MySQL с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90906563"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Краткое руководство. Создание базы данных Azure для сервера MySQL с помощью портала Azure

База данных Azure для MySQL — это управляемая служба, которая позволяет запускать, администрировать и масштабировать высокодоступные базы данных MySQL в облаке. В этом кратком руководстве описывается, как за пять минут создать сервер службы "База данных Azure для MySQL" с помощью портала Azure.  

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure
В браузере откройте [портал Azure](https://portal.azure.com/). Введите свои учетные данные для входа на портал. Панель мониторинга службы является представлением по умолчанию.

## <a name="create-an-azure-database-for-mysql-server"></a>Создайте сервер базы данных Azure для MySQL.
Сервер базы данных Azure для MySQL создается с определенным набором [вычислительных ресурсов и ресурсов хранения](./concepts-compute-unit-and-storage.md). Он создается в [группе ресурсов Azure](../azure-resource-manager/management/overview.md).

Чтобы создать базу данных Azure для сервера MySQL, сделайте следующее:

1. Выберите **Создать ресурс** (+) в левом верхнем углу страницы портала.

2. Выберите **Базы данных** > **База данных Azure для MySQL**. Чтобы найти службу, вы также можете ввести в поле поиска **MySQL**.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Параметр базы данных Azure для MySQL":::

3. Заполните форму для создания сервера, указав следующую информацию:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Параметр базы данных Azure для MySQL" или "Оптимизировано для памяти". 

4. Щелкните **Просмотр и создание**, чтобы подготовить сервер. Этот процесс может занять до 20 минут.
   
5. На панели инструментов щелкните **Уведомления** (значок колокольчика), чтобы отслеживать процесс развертывания.
   
По умолчанию на сервере создаются следующие базы данных: **information_schema**, **mysql**, **performance_schema** и **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Настройка правила брандмауэра на уровне сервера
По умолчанию созданный сервер защищен с помощью брандмауэра и не является общедоступным. Чтобы предоставить разрешение на доступ с IP-адреса, перейдите к ресурсу сервера на портале Azure и выберите **Безопасность подключения** в левой части меню для этого ресурса сервера. Если вы не знаете, как найти ресурс, см. раздел об [открытии ресурса](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Параметр базы данных Azure для MySQL":::
   
Теперь выберите **Добавить текущий IP-адрес клиента** и щелкните **Сохранить**. Вы можете добавить дополнительные IP-адреса или указать диапазон адресов, чтобы подключаться к серверу с любого из этих IP-адресов. Дополнительные сведения см. в статье об [управлении правилами брандмауэра для сервера Базы данных Azure для MySQL](./concepts-firewall-rules.md).

> [!NOTE]
> Проверьте, разрешает ли сеть исходящий трафик на порт 3306, который использует служба "База данных Azure для MySQL", чтобы избежать проблем с подключением.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Подключение к серверу службы "База данных Azure для MySQL" с помощью клиента командной строки mysql
Вы можете выбрать [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) или [MySQL Workbench](./connect-workbench.md), чтобы подключиться к серверу из локальной среды. В этом кратком руководстве мы запустим **mysql.exe** в [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) для подключения к серверу.

1. Запустите Azure Cloud Shell на портале, щелкнув выделенный значок в верхней левой части экрана. Запишите имя нового сервера, имя администратора этого сервера, пароль и подписку из раздела **Обзор**, как показано на рисунке ниже.

    >[!NOTE]
    >Если вы запускаете Cloud Shell первый раз, отобразится запрос на создание группы ресурсов и учетной записи хранения. Это одноразовое действие, которое автоматически применяется для всех сеансов. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Параметр базы данных Azure для MySQL":::
2. Выполните эту команду в терминале Azure Cloud Shell. Замените предложенные значения реальными именами сервера и администратора. Для имени администратора требуется указать "@\<servername>", как показано ниже для службы "База данных Azure для MySQL".  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Вот так выглядит этот процесс в терминале Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. В том же терминале Azure Cloud Shell создайте базу данных **guest**. 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Перейдите к базе данных **guest**.
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Введите ```quit``` и нажмите клавишу ВВОД, чтобы выйти из mysql.   

## <a name="clean-up-resources"></a>Очистка ресурсов
Вы успешно создали сервер службы "База данных Azure для MySQL" в группе ресурсов.  Если вы ожидаете, что в будущем эти ресурсы вам не понадобятся, их можно удалить, удалив группу ресурсов или сам сервер MySQL. Чтобы удалить группу ресурсов, выполните следующие шаги.
1. Войдите на портал Azure; найдите в поиске и выберите **Группы ресурсов**. 
2. В списке групп ресурсов выберите имя нужной группы ресурсов.
3. На странице "Обзор" для группы ресурсов выберите **Удалить группу ресурсов**.
4. В диалоговом окне подтверждения введите имя группы ресурсов и щелкните **Удалить**.

Чтобы удалить сервер, можно нажать кнопку **Удалить** на странице **Обзор** для этого сервера, как показано ниже.
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Параметр базы данных Azure для MySQL":::

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
>[Создание приложения PHP в Windows с помощью MySQL](../app-service/app-service-web-tutorial-php-mysql.md)
>[Создание приложения PHP в Linux с помощью MySQL](../app-service/containers/tutorial-php-mysql-app.md)
>[Создание приложения Spring на основе Java с помощью MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
