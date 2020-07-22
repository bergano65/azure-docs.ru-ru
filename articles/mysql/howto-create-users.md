---
title: Создание пользователей с базой данных Azure для MySQL
description: В этой статье описывается создание учетных записей пользователей для взаимодействия с сервером базы данных Azure для MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/2/2020
ms.openlocfilehash: e3616e5f86c9f73eec8fceaca20f149ec1e09b9a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118602"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Создание пользователей на сервере базы данных Azure для MySQL

В этой статье описывается создание пользователей на сервере базы данных Azure для MySQL.

> [!NOTE]
> Обмен данными без смещения
>
> Корпорация Майкрософт поддерживает различные и включенные среды. Эта статья содержит ссылки на слово _Slave_. В соответствии с [руководством по стилю Майкрософт для обмена данными без](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) пересчета этот термин распознается как исключение. Это слово используется в этой статье для обеспечения согласованности, так как в настоящее время это слово, которое отображается в программном обеспечении. При обновлении программного обеспечения для удаления слова эта статья будет обновлена для выравнивания.
>

При создании первой базы данных Azure для MySQL вы указали имя пользователя и пароль администратора сервера, используемые для входа. Дополнительные сведения см. в [этом кратком руководстве](quickstart-create-mysql-server-database-using-azure-portal.md). Имя пользователя администратора сервера можно найти на портале Azure.

Администратор сервера получает определенные привилегии для выполнения следующих операций на сервере: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER.

После создания сервера базы данных Azure для MySQL с помощью первой учетной записи администратора сервера можно создать дополнительных пользователей и предоставить им права администратора. Кроме того, учетная запись администратора сервера может использоваться для создания менее привилегированных пользователей, имеющих доступ к отдельным схемам базы данных.

> [!NOTE]
> Роль SUPER Privilege и DBA не поддерживаются. Проверьте [права](concepts-limits.md#privilege-support) в статье ограничения, чтобы понять, что не поддерживается в службе.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Как создать дополнительных администраторов в базе данных Azure для MySQL

1. Получите сведения о подключении и имя пользователя администратора.
   Чтобы подключиться к серверу базы данных, вам потребуются учетные данные администратора для входа и полное имя сервера. Вы можете легко найти данные для входа на странице **Обзор** сервера или на странице **Свойства** на портале Azure.

2. Используйте учетную запись и пароль администратора для подключения к серверу базы данных. Используйте предпочитаемый клиентский инструмент, например MySQL Workbench, mysql.exe, HeidiSQL и т. д.
   Если вы не знаете, как подключиться, прочитайте раздел [База данных Azure для MySQL: подключение и запрос данных с помощью MySQL Workbench](./connect-workbench.md).

3. Измените и выполните следующий код SQL. Замените новым именем пользователя значение заполнителя `new_master_user`. Эта синтаксическая конструкция предоставляет перечисленные привилегии для всех схем базы данных (*.*) имени пользователя (new_master_user в этом примере).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Проверка разрешений

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Как создать пользователей базы данных в базе данных Azure для MySQL

1. Получите сведения о подключении и имя пользователя администратора.
   Чтобы подключиться к серверу базы данных, вам потребуются учетные данные администратора для входа и полное имя сервера. Вы можете легко найти данные для входа на странице **Обзор** сервера или на странице **Свойства** на портале Azure.

2. Используйте учетную запись и пароль администратора для подключения к серверу базы данных. Используйте предпочитаемый клиентский инструмент, например MySQL Workbench, mysql.exe, HeidiSQL и т. д.
   Если вы не знаете, как подключиться, прочитайте раздел [База данных Azure для MySQL: подключение и запрос данных с помощью MySQL Workbench](./connect-workbench.md).

3. Измените и выполните следующий код SQL. Замените значение заполнителя `db_user` новым именем пользователя, а значение заполнителя `testdb` — именем базы данных.

   Этот код SQL создает базу данных testdb для примера. Затем он создает пользователя в службе MySQL и предоставляет ему все привилегии для новой схемы базы данных (testdb.\*).

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Проверьте предоставление привилегий в базе данных.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Войдите на сервер, указав эту базу данных и введя новое имя пользователя и пароль. В этом примере показана командная строка MySQL. После ввода этой команды вам будет предложено ввести пароль для имени пользователя. Укажите собственные имя сервера, имя базы данных и имя пользователя.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Дальнейшие действия

Откройте брандмауэр для IP-адресов компьютеров новых пользователей, чтобы обеспечить их подключение. Для этого ознакомьтесь с разделом [Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure](howto-manage-firewall-using-portal.md) или [Azure CLI](howto-manage-firewall-using-cli.md).

Чтобы получить дополнительные сведения об управлении учетными записями пользователей, ознакомьтесь с [управлением учетными записями пользователей](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [синтаксисом GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html) и [привилегиями](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html) в документации по продукту MySQL.
