---
title: Использование Python для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/28/2019
ms.openlocfilehash: b611eb02203c872e3497b5b7c12acddd9eab14c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188394"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Python для создания запросов к базе данных SQL Azure

 В этом кратком руководстве показано, как использовать [Python](https://python.org) для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных. Дополнительные сведения о пакете SDK см. в [справочной документации](https://docs.microsoft.com/python/api/overview/azure/sql). См. также [репозиторий pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) на GitHub и [пример pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Python и связанное программное обеспечение для используемой операционной системы:
  
  - **macOS**. Установите Homebrew, Python, драйвер ODBC и SQLCMD, а затем драйвер Python для SQL Server. См. шаги в разделе 1.2, 1.3 и 2.1 в статье о [создании приложений Python с помощью SQL Server в macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Дополнительные сведения см. в разделе об [установке драйвера Microsoft ODBC в Linux и macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu**: Установите Python и другие необходимые пакеты с помощью `sudo apt-get install python python-pip gcc g++ build-essential`. Скачайте и установите драйвер ODBC, SQLCMD и драйвер Python для SQL Server. Инструкции см. в разделе о [настройке среды разработки для разработки на Python с помощью pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows**: Установите Python, драйвер ODBC, SQLCMD и драйвер Python для SQL Server. Инструкции см. в разделе о [настройке среды разработки для разработки на Python с помощью pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
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

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Сведения о драйверах Microsoft Python для SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Центр по разработке на Python](https://azure.microsoft.com/develop/python/?v=17.23h)

