---
title: Использование Visual Studio c .NET и C# для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: Здесь показано, как использовать Visual Studio для создания приложения C#, которое подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4472ff10c42cd9163693e7316b6bdaef50258db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569245"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Краткое руководство. Использование .NET и C# в Visual Studio для подключения и создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [платформу .NET](https://www.microsoft.com/net/) и код C# в Visual Studio для отправки запросов к базе данных SQL Azure с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- База данных SQL Azure. Для создания и настройки базы данных в службе "База данных SQL Azure" можно использовать одно из этих кратких руководств.

  || Отдельная база данных | Управляемый экземпляр |
  |:--- |:--- |:---|
  | Создание| [Портал](sql-database-single-database-get-started.md) | [Портал](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Настройка | [Правило брандмауэра для IP-адресов на уровне сервера](sql-database-server-level-firewall-rule.md)| [Подключение из виртуальной машины](sql-database-managed-instance-configure-vm.md)|
  |||[Подключение "точка — сеть"](sql-database-managed-instance-configure-p2s.md)
  |Загрузка данных|База данных Adventure Works, загруженная для краткого руководства|[Восстановление базы данных Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Восстановление или импорт Adventure Works из файла [BACPAC](sql-database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Скрипты в этой статье предназначены для использования базы данных Adventure Works. Используя управляемый экземпляр, необходимо импортировать базу данных Adventure Works в базу данных экземпляра или изменить скрипты в этой статье для использования базы данных Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) — выпуск Community, Professional или Enterprise.

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**. 

## <a name="create-code-to-query-the-sql-database"></a>Создание кода для запроса базы данных SQL

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**. 
   
1. В диалоговом окне **Новый проект** выберите **Visual C#** , а затем — **Консольное приложение (.NET Framework)** .
   
1. Введите название проекта *sqltest*, а затем нажмите кнопку **ОК**. Создается новый проект. 
   
1. Выберите **Проект** > **Управление пакетами NuGet**. 
   
1. В **диспетчер пакетов NuGet** выберите вкладку **Обзор**, а затем найдите и выберите **System.Data.SqlClient**.
   
1. На странице **System.Data.SqlClient** щелкните **Установить**. 
   - При выводе запроса нажмите кнопку **ОК**, чтобы продолжить установку. 
   - Если откроется окно **Прием условий лицензионного соглашения**, выберите **Я принимаю**.
   
1. После завершения установки вы можете закрыть **диспетчер пакетов NuGet**. 
   
1. В редакторе кода замените содержимое **Program.cs** следующим кодом. Замените значения `<server>`, `<username>`, `<password>` и `<database>`.
   
   >[!IMPORTANT]
   >Данный пример кода использует данные AdventureWorksLT, которые можно выбрать в качестве источника при создании базы данных. Если в вашей базе данных содержатся другие данные, используйте таблицы из собственной базы данных в запросе SELECT. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Выполнение кода

1. Чтобы запустить приложение, выберите **Отладка** > **Начать отладку** или **Запустить** на панели инструментов либо нажмите клавишу **F5**.
1. Убедитесь, что первые 20 строк с продуктами в категории возвращены из вашей базы данных и закройте окно приложения.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [подключиться и отправить запрос к базе данных SQL Azure с помощью .NET Core](sql-database-connect-query-dotnet-core.md) в Windows, Linux и macOS.  
- См. дополнительные сведения о [начале работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli).
- Узнайте, как спроектировать первую базу данных SQL с помощью [SSMS](sql-database-design-first-database.md) или [.NET](sql-database-design-first-database-csharp.md).
- Дополнительные сведения о .NET см. в [этой документации](https://docs.microsoft.com/dotnet/).
- Пример логики повтора: [Выполнение устойчивого подключения к SQL с помощью ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

