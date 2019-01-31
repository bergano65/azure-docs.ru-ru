---
title: Использование Visual Studio c .NET и C# для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: Здесь показано, как использовать Visual Studio для создания приложения C#, которое подключается к Базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 93249b7d274ce9d7928dfa46eb339da68c92b785
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163302"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Краткое руководство. Использование .NET и C# в Visual Studio для подключения и создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [платформу .NET](https://www.microsoft.com/net/) и код C# в Visual Studio для отправки запросов к базе данных SQL Azure с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) — выпуски Community, Professional или Enterprise.

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Создание кода для запроса базы данных SQL

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**. 
   
1. В диалоговом окне **Новый проект** выберите **Visual C#**, а затем — **Консольное приложение (.NET Framework)**.
   
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
- Пример логики повтора: [Шаг 4. Выполнение устойчивого подключения к SQL с помощью ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

