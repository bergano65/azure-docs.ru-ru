---
title: Использование Visual Studio с .NET и C# для отправки запросов
description: Использование Visual Studio для создания приложения C#, которое подключается к базе данных в службе "База данных SQL Azure" или Управляемом экземпляре SQL Azure, и отправляет запросы.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 497de36d63a909d2c53374482dfe09d1f19dfded
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033125"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Краткое руководство. Использование .NET и C# в Visual Studio для подключения к базе данных в службе "База данных SQL Azure" или Управляемом экземпляре SQL Azure и отправке запросов.
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В этом кратком руководстве показано, как использовать [платформу .NET](https://www.microsoft.com/net/) и код C# в Visual Studio для отправки запросов к базе данных в службе "База данных SQL Azure" с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
- База данных в службе "База данных SQL Azure". Для создания и настройки базы данных в службе "База данных SQL Azure" можно использовать одно из этих кратких руководств.

  | Действие | База данных SQL | Управляемый экземпляр SQL | SQL Server на виртуальной машине Azure |
  |:--- |:--- |:---|:---|
  | Создание| [Портал](single-database-create-quickstart.md) | [Портал](../managed-instance/instance-create-quickstart.md) | [Портал](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configure | [Правило брандмауэра для IP-адресов на уровне сервера](firewall-create-server-level-portal-quickstart.md)| [Подключение из виртуальной машины](../managed-instance/connect-vm-instance-configure.md)|
  |||[Подключение из локальной сети](../managed-instance/point-to-site-p2s-configure.md) | [Диалоговое окно "Подключение к SQL Server"](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Загрузка данных|База данных Adventure Works, загруженная для краткого руководства|[Восстановление базы данных Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Восстановление базы данных Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Восстановление или импорт Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Восстановление или импорт Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Скрипты в этой статье предназначены для использования базы данных Adventure Works. Используя Управляемый экземпляр SQL, необходимо импортировать базу данных Adventure Works в базу данных экземпляра или изменить скрипты в этой статье для использования базы данных Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) — выпуск Community, Professional или Enterprise.

## <a name="get-server-connection-information"></a>Получение сведений о подключении к серверу

Получите сведения, необходимые для подключения к базе данных. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для базы данных в службе "База данных SQL Azure" или полное имя сервера (либо IP-адрес) рядом с полем **Узел** для Управляемого экземпляра SQL Azure или SQL Server на виртуальной машине Azure. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

> [!NOTE]
> Сведения о подключении к экземпляру SQL Server на виртуальной машине Azure см. в разделе [Подключение к экземпляру SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Создание кода для запроса к базе данных в службе "База данных SQL Azure"

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**. 
   
1. В диалоговом окне **Новый проект** выберите **Visual C#** , а затем — **Консольное приложение (.NET Framework)** .
   
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
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [подключиться и отправить запрос к базе данных в службе "База данных SQL Azure" с помощью .NET Core](connect-query-dotnet-core.md) в Windows, Linux и macOS.  
- См. дополнительные сведения о [начале работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli).
- См. дополнительные сведения в разделах [Руководство по разработке первой базы данных в службе "База данных SQL Azure" с помощью SSMS](design-first-database-tutorial.md) или [Руководство по разработке первой базы данных в службе "База данных SQL Azure" с помощью .NET](design-first-database-csharp-tutorial.md).
- Дополнительные сведения о .NET см. в [этой документации](https://docs.microsoft.com/dotnet/).
- Пример логики повтора: [Выполнение устойчивого подключения к Azure SQL с помощью ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

