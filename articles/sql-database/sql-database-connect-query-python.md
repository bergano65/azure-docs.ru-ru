---
title: Использование Python для создания запросов к базе данных
description: В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768576"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Python для создания запросов к базе данных SQL Azure

В этом кратком руководстве вы будете использовать Python для подключения к базе данных SQL Azure, а затем выполните запрос данных с помощью инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [База данных SQL Azure](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 и связанное с ним программное обеспечение

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Для установки Homebrew и Python, драйвера ODBC и SQLCMD, а также драйвера Python для SQL Server используйте шаги **1.2**, **1.3** и **2.1** в [create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) (Создание приложений Python с помощью SQL Server на MacOS).

  Дополнительные сведения см. в статье [Installing the Microsoft ODBC Driver for SQL Server on Linux and macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) (Установка драйвера ODBC для SQL Server на Linux и macOS).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Используйте `sudo apt-get install python python-pip gcc g++ build-essential`, чтобы установить Python и другие необходимые пакеты.

  Чтобы установить драйвер ODBC, SQLCMD и драйвер Python для SQL Server, см. статью [Step 1: Configure development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) (Шаг 1. Настройка среды разработки для использования pyodbc в Python).

  Дополнительные сведения см. в статье [Installing the Microsoft ODBC Driver for SQL Server on Linux and macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) (Установка драйвера ODBC для SQL Server на Linux и macOS).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Чтобы установить Python, драйвер ODBC, SQLCMD и драйвер Python для SQL Server, см. статью [Step 1: Configure development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) (Шаг 1. Настройка среды разработки для использования pyodbc в Python).

  Дополнительные сведения см. в статье [Microsoft ODBC Driver for SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) (Драйвер ODBC Microsoft для SQL Server).

---

> [!IMPORTANT]
> Скрипты в этой статье предназначены для использования базы данных **Adventure Works**.

> [!NOTE]
> Вы также можете использовать управляемый экземпляр SQL Azure.
>
> Для создания и настройки используйте [портал Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), или [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), затем установите подключение к [локальному серверу](sql-database-managed-instance-configure-p2s.md) или [виртуальной машине](sql-database-managed-instance-configure-vm.md).
>
> Для загрузки данных см. [Quickstart: Import a BACPAC file to a database in Azure SQL Database](sql-database-import.md) (Краткое руководство. Импорт BACPAC-файла в базу данных с помощью Azure SQL Database), а также сведения о файле [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) или [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](sql-database-managed-instance-get-started-restore.md).

Для дальнейшего изучения Python и базы данных SQL Azure см. [Библиотеки Базы данных SQL Azure для Python](/python/api/overview/azure/sql), репозитория [репозиторий pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) и [выборку pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

## <a name="create-code-to-query-your-sql-database"></a>Создание кода для запроса базы данных SQL 

1. Создайте файл *sqltest.py* в текстовом редакторе.  
   
1. Добавьте следующий код. Подставьте собственные значения сервера, базы данных, имени пользователя и пароля вместо значений \<server>, \<database>, \<username> и \<password>.
   
   >[!IMPORTANT]
   >Данный пример кода использует данные AdventureWorksLT, которые можно выбрать в качестве источника при создании базы данных. Если в вашей базе данных содержатся другие данные, используйте таблицы из собственной базы данных в запросе SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующую команду:

   ```cmd
   python sqltest.py
   ```

1. Убедитесь, что возвращены первые 20 строк с продуктами из категории, и закройте командное окно.

## <a name="next-steps"></a>Дальнейшие действия

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Сведения о драйверах Microsoft Python для SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Центр по разработке на Python](https://azure.microsoft.com/develop/python/?v=17.23h)

