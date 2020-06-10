---
title: Использование Java для выполнения запросов к базе данных
description: Объясняет, как с помощью Java создать программу для подключения к базе данных в составе Базы данных SQL Azure или Управляемому экземпляру SQL Azure и запрашивать из этих служб данные с использованием инструкций T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267398"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Краткое руководство. Использование Java для отправки запросов к базе данных в службе "База данных SQL Azure" или Управляемому экземпляру SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В этом кратком руководстве вы будете использовать Java для подключения к базе данных в составе Базы данных SQL Azure или Управляемому экземпляру SQL Azure, а затем выполните запрос данных с помощью инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.

  || База данных SQL | Управляемый экземпляр SQL | SQL Server на виртуальной машине Azure |
  |:--- |:--- |:---|:---|
  | Создание| [Портал](single-database-create-quickstart.md) | [Портал](../managed-instance/instance-create-quickstart.md) | [Портал](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configure | [Правило брандмауэра для IP-адресов на уровне сервера](firewall-create-server-level-portal-quickstart.md)| [Подключение из виртуальной машины](../managed-instance/connect-vm-instance-configure.md)|
  |||[Подключение из локальной сети](../managed-instance/point-to-site-p2s-configure.md) | [Подключение к экземпляру SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Загрузка данных|База данных Adventure Works, загруженная для краткого руководства|[Восстановление базы данных Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Восстановление базы данных Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Восстановление или импорт базы данных Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Восстановление или импорт базы данных Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- Программное обеспечение, связанное с [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macos"></a>[macOS](#tab/macos)

  Установите Homebrew и Java, а затем Maven, выполнив действия **1.2** и **1.3**, описанные в статье [Создание приложений Java с помощью SQL Server на macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Установите Java, пакет SDK для Java, а затем Maven, выполнив действия **1.2**, **1.3** и **1.4**, описанные в статье [Создание приложений Java с помощью SQL Server на Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Установите Java, а затем Maven, выполнив действия **1.2** и **1.3**, описанные в статье [Создание приложений Java с помощью SQL Server на Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Скрипты в этой статье предназначены для использования базы данных **Adventure Works**.

> [!NOTE]
> Вы также можете использовать Управляемый экземпляр SQL Azure.
>
> Для создания и настройки используйте [портал Azure](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) или [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), затем установите подключение к [локальному серверу](../managed-instance/point-to-site-p2s-configure.md) или [виртуальной машине](../managed-instance/connect-vm-instance-configure.md).
>
> Для загрузки данных см. [Quickstart: Import a BACPAC file to a database in Azure SQL Database](database-import.md) (Краткое руководство. Импорт BACPAC-файла в базу данных с помощью Azure SQL Database), а также сведения о файле [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) или [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Получение сведений о подключении к серверу

Получите сведения, необходимые для подключения к базе данных в составе Базы данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите **Базы данных SQL** или перейдите на страницу **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для базы данных в службе "База данных SQL Azure" или полное имя сервера (либо IP-адрес) рядом с полем **Узел** для Управляемого экземпляра SQL Azure или сервера SQL Server на виртуальной машине Azure. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

> [!NOTE]
> Сведения о подключении для сервера SQL Server на виртуальной машине Azure см. в [этой статье](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

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

## <a name="add-code-to-query-the-database"></a>Добавление кода для создания запроса к базе данных

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
   > В примере кода используется образец базы данных **AdventureWorksLT** в службе "База данных SQL Azure".

## <a name="run-the-code"></a>Выполнение кода

1. Запустите приложение в командной строке.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Убедитесь, что возвращены первые 20 строк, и закройте окно приложения.

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка первой базы данных в Базе данных Azure SQL](design-first-database-tutorial.md)  
- [Драйвер Microsoft JDBC для SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Сообщите о проблеме или задайте вопросы](https://github.com/microsoft/mssql-jdbc/issues)  
