---
title: Использование Ruby для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Ruby для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/02/2019
ms.openlocfilehash: 30174051c0d26595c65a79724cf3d08465a4cab7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566984"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Ruby для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [Ruby](https://www.ruby-lang.org) для подключения к базе данных SQL Azure и выполнения запроса данных с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством необходимо следующее:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Ruby и связанное программное обеспечение для используемой операционной системы:
  
  - **macOS**. установите Homebrew, rbenv и ruby-build, Ruby, FreeTDS и TinyTDS. См. шаги в разделе 1.2, 1.3, 1.4, 1.5 и 2.1 в статье [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) (Создание приложений Ruby с помощью SQL Server в macOS).
  
  - **Ubuntu**: установите необходимые компоненты для Ruby, rbenv и ruby-build, Ruby, FreeTDS и TinyTDS. См. шаги в разделе 1.2, 1.3, 1.4, 1.5 и 2.1 в статье о [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) (Создание приложений Ruby с помощью SQL Server в Ubuntu).
  
  - **Windows**: установите Ruby, Ruby Devkit и TinyTDS. См. [Configure development environment for Ruby development](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development) (Настройка среды разработки Ruby).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>Создание кода для запроса базы данных SQL

1. Создайте файл *sqltest.rb* в текстовом редакторе и редакторе кода.
   
1. Добавьте следующий код. Замените значения из вашей базы данных SQL Azure на `<server>`, `<database>`, `<username>` и `<password>`.
   
   >[!IMPORTANT]
   >Данный пример кода использует данные AdventureWorksLT, которые можно выбрать в качестве источника при создании базы данных. Если в вашей базе данных содержатся другие данные, используйте таблицы из собственной базы данных в запросе SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующую команду:

   ```bash
   ruby sqltest.rb
   ```
   
1. Убедитесь, что первые 20 строк с продуктами в категории возвращены из вашей базы данных. 

## <a name="next-steps"></a>Дополнительная информация
- [Руководство. Разработка первой базы данных SQL Azure с помощью SSMS](sql-database-design-first-database.md).
- [TinyTDS - Simple and fast FreeTDS bindings for Ruby using DB-Library.](https://github.com/rails-sqlserver/tiny_tds) (TinyTDS — простая и быстрая привязка FreeTDS для Ruby с помощью DB-Library).
- Сообщите о проблемах или задайте вопросы по TinyTDS [здесь](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Драйвер Ruby для SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
