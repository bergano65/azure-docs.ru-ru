---
title: Использование Java для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как с помощью Java создать программу, которая подключается к базе данных SQL Azure, и запрашивать из нее данные с использованием инструкций T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/20/2018
ms.openlocfilehash: 5c12bd54c0ea96ac915fedab94f03cf044330dcf
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723301"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Краткое руководство. Создание запросов к базе данных SQL Azure с использованием Java

В этой статье показано, как подключаться к базе данных SQL Azure с помощью [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Затем можно запросить данные с использованием инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого примера понадобится следующее:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра на уровне сервера](sql-database-get-started-portal-firewall.md) для общедоступного IP-адреса используемого компьютера.

- Установленное связанное с Java программное обеспечение для используемой операционной системы:

  - **MacOS.** Установите Homebrew и Java, а затем Maven. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu.** Установите Java, пакет JDK и Maven. Ознакомьтесь с шагами 1.2, 1.3 и 1.4 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows.** Установите Java и Maven. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-database-connection"></a>Подключение к базе данных

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)  

- [Microsoft JDBC Driver для SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Сообщите о проблеме или задайте вопросы](https://github.com/microsoft/mssql-jdbc/issues)  
