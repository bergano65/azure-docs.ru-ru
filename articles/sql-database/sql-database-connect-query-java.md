---
title: Использование Java для выполнения запросов к базе данных
description: В этой статье показано, как с помощью Java создать программу, которая подключается к базе данных SQL Azure, и запрашивать из нее данные с использованием инструкций T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768643"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Java для создания запросов к базе данных SQL Azure

В этом кратком руководстве вы будете использовать Java для подключения к базе данных SQL Azure, а затем выполните запрос данных с помощью инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [База данных SQL Azure](sql-database-single-database-get-started.md)
- Программное обеспечение, связанное с [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Установите Homebrew и Java, а затем Maven, выполнив действия **1.2** и **1.3** статьи [Create Java apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) (Создание приложений Java с помощью SQL Server в macOS).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Установите Java, комплект SDK для Java, а затем Maven, выполнив действия **1.2**, **1.3** и **1.4** статьи [Create Java apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) (Создание приложений Java с помощью SQL Server на Ubuntu).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Установите Java, а затем Maven, выполнив действия **1.2** и **1.3** статьи [Create Java apps using SQL Server on Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) (Создание приложений Java с помощью SQL Server на Windows).

  ---

> [!IMPORTANT]
> Скрипты в этой статье предназначены для использования базы данных **Adventure Works**.

> [!NOTE]
> Вы также можете использовать управляемый экземпляр SQL Azure.
>
> Для создания и настройки используйте [портал Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), или [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), затем установите подключение к [локальному серверу](sql-database-managed-instance-configure-p2s.md) или [виртуальной машине](sql-database-managed-instance-configure-vm.md).
>
> Для загрузки данных см. [Quickstart: Import a BACPAC file to a database in Azure SQL Database](sql-database-import.md) (Краткое руководство. Импорт BACPAC-файла в базу данных с помощью Azure SQL Database), а также сведения о файле [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) или [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите **Базы данных SQL** или перейдите на страницу **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**. 

## <a name="create-the-project"></a>Создание проекта

1. В командной строке создайте проект Maven с именем *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Перейдите в папку *sqltest* и откройте *pom.xml* с помощью предпочитаемого текстового редактора. Добавьте **Microsoft JDBC Driver для SQL Server** к зависимостям проекта, используя следующий код:

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. В файле *pom.xml* добавьте в проект указанные ниже свойства. Если у вас нет раздела свойств, его можно добавить после зависимостей.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Сохраните и закройте файл *pom.xml*.

## <a name="add-code-to-query-database"></a>Добавление кода для создания запроса к базе данных

1. В проекте Maven уже должен быть файл с именем *App.java*, расположенный в этой папке:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Откройте его и замените имеющееся содержимое следующим кодом. Затем добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > В примере кода используется образец базы данных **AdventureWorksLT** для SQL Azure.

## <a name="run-the-code"></a>Выполнение кода

1. Запустите приложение в командной строке.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Убедитесь, что возвращены первые 20 строк, и закройте окно приложения.

## <a name="next-steps"></a>Дальнейшие действия

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)  

- [Драйвер Microsoft JDBC для SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Сообщите о проблеме или задайте вопросы](https://github.com/microsoft/mssql-jdbc/issues)  
