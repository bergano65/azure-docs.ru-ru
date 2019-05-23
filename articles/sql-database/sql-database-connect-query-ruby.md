---
title: Использование Ruby для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Ruby для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: d674928bbe585174db897b2a052a5fd09bcee329
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792069"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Ruby для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [Ruby](https://www.ruby-lang.org) для подключения к базе данных SQL Azure и выполнения запроса данных с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством необходимо следующее:

- База данных SQL Azure. Для создания и настройки базы данных в службе "База данных SQL Azure" можно использовать одно из этих кратких руководств.

  || Отдельная база данных | Управляемый экземпляр |
  |:--- |:--- |:---|
  | Создание| [Портал](sql-database-single-database-get-started.md) | [Портал](sql-database-managed-instance-get-started.md) |
  || [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](scripts/sql-database-create-and-configure-database-cli.md) | [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Настройка | [Правило брандмауэра для IP-адресов на уровне сервера](sql-database-server-level-firewall-rule.md)| [Подключение из виртуальной машины](sql-database-managed-instance-configure-vm.md)|
  |||[Подключение "точка — сеть"](sql-database-managed-instance-configure-p2s.md)
  |Загрузка данных|База данных Adventure Works, загруженная для краткого руководства|[Восстановление базы данных Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Восстановление или импорт Adventure Works из файла [BACPAC](sql-database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Скрипты в этой статье предназначены для использования базы данных Adventure Works. Используя управляемый экземпляр, необходимо импортировать базу данных Adventure Works в базу данных экземпляра или изменить скрипты в этой статье для использования базы данных Wide World Importers.
  
- Ruby и связанное программное обеспечение для используемой операционной системы:
  
  - **macOS**. установите Homebrew, rbenv и ruby-build, Ruby, FreeTDS и TinyTDS. См. шаги в разделе 1.2, 1.3, 1.4, 1.5 и 2.1 в статье [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) (Создание приложений Ruby с помощью SQL Server в macOS).
  
  - **Ubuntu**: установите необходимые компоненты для Ruby, rbenv и ruby-build, Ruby, FreeTDS и TinyTDS. См. шаги в разделе 1.2, 1.3, 1.4, 1.5 и 2.1 в статье о [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) (Создание приложений Ruby с помощью SQL Server в Ubuntu).
  
  - **Windows**: установите Ruby, Ruby Devkit и TinyTDS. См. [Configure development environment for Ruby development](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development) (Настройка среды разработки Ruby).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**. 

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
