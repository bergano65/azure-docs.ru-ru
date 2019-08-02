---
title: Реализация решения географически распределенной базы данных SQL Azure | Документация Майкрософт
description: Сведения о настройке базы данных SQL Azure и приложения для отработки отказа в реплицированную базу данных и тестовой отработки отказа.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4a21fe3ed15d1dc2550f6863611b27d2b36c5c51
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568096"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Учебник. Реализация географически распределенной базы данных

Настройте базу данных SQL Azure и приложение для отработки отказа в удаленный регион, а затем протестируйте план отработки отказа. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - создавать [группу отработки отказа](sql-database-auto-failover-group.md);
> - Запуск приложения Java для запроса базы данных SQL Azure
> - Тестовая отработка отказа

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

Для работы с этим руководством необходимо убедиться, что установлены следующие компоненты:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Отдельная база данных в базе данных SQL Azure. Чтобы создать ее, можно использовать:
  - [Портал](sql-database-single-database-get-started.md)
  - [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > В этом руководстве используется пример базы данных *AdventureWorksLT*.

- Сведения для Java и Maven можно просмотреть в разделе [Создание приложения с помощью SQL Server](https://www.microsoft.com/sql-server/developer-get-started/). Для этого выделите **Java** и выберите среду, а затем следуйте инструкциям.

> [!IMPORTANT]
> Убедитесь, что правила брандмауэра настроены для использования общедоступного IP-адреса компьютера, на котором выполняются действия из этого руководства. Правила брандмауэра уровня базы данных автоматически реплицируются на сервер-получатель.
>
> Дополнительные сведения см. в статье [sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) (sp_set_database_firewall_rule (база данных SQL Azure)). Чтобы определить IP-адрес, используемый для правила брандмауэра уровня сервера для компьютера, обратитесь к статье [Краткое руководство. Создание правила брандмауэра на уровне сервера для базы данных SQL с помощью портала Azure](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Создание группы отработки отказа

С помощью Azure PowerShell создайте [группы отработки отказа](sql-database-auto-failover-group.md) между имеющимся сервером Azure SQL и новым сервером Azure SQL в другом регионе. Затем добавьте пример базы данных в группу отработки отказа.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Чтобы создать группу отработки отказа, выполните следующий сценарий:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Параметры георепликации можно также изменить на портале Azure, выбрав нужную базу данных и щелкнув **Параметры** > **Георепликация**.

![Параметры георепликации](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Запуск примера проекта

1. В консоли создайте проект Maven с помощью следующей команды:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Введите **Y** и нажмите клавишу **ВВОД**.

1. Перейдите в каталог с новым проектом.

   ```bash
   cd SqlDbSample
   ```

1. Используя любой удобный редактор, откройте файл *pom.xml* в папке проекта.

1. Добавьте Microsoft JDBC Driver для зависимости SQL Server, добавив следующий раздел `dependency`. Эту зависимость необходимо вставить в рамках большего раздела `dependencies`.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Укажите версию Java, добавив раздел `properties` после раздела `dependencies`:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Поддержите файлы манифеста, добавив раздел `build` после раздела `properties`:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Сохраните и закройте файл *pom.xml*.

1. Откройте файл *App.java*, расположенный в ..\SqlDbSample\src\main\java\com\sqldbsamples, и замените его содержимое кодом ниже.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Сохраните и закройте файл *App.java*.

1. В командной консоли выполните следующую команду:

   ```bash
   mvn package
   ```

1. Запустите приложение, которое будет выполняться около часа до остановки вручную. Таким образом у вас будет время для выполнения теста отработки отказа.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Тестовая отработка отказа

Запустите следующие скрипты для имитации отработки отказа и просмотрите результаты приложения. Обратите внимание, как некоторые операции вставки и выбора завершатся ошибкой во время миграции базы данных.

Во время теста вы также можете проверить роль сервера аварийного восстановления с помощью следующей команды:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Чтобы проверить отработку отказа, выполните следующие действия.

1. Запустите ручную отработку отказа группы отработки отказа.

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Восстановите группу отработки отказа на основной сервер:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы настроили базу данных SQL Azure и приложение для отработки отказа в удаленный регион и протестировали план отработки отказа. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> - создавать группу отработки отказа георепликации;
> - Запуск приложения Java для запроса базы данных SQL Azure
> - Тестовая отработка отказа

Перейдите к следующему руководству о миграции с помощью DMS.

> [!div class="nextstepaction"]
> [Руководство. Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS в автономном режиме](../dms/tutorial-sql-server-to-managed-instance.md)
