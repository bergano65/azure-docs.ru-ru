---
title: Использование Python для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7fbb9265ac12126fb13a26650fbb5d65f3d39260
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792079"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Python для создания запросов к базе данных SQL Azure

 В этом кратком руководстве показано, как использовать [Python](https://python.org) для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных. Дополнительные сведения о пакете SDK см. в [справочной документации](https://docs.microsoft.com/python/api/overview/azure/sql). См. также [репозиторий pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) на GitHub и [пример pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

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
  
- Python и связанное программное обеспечение для используемой операционной системы:
  
  - **macOS**. Установите Homebrew, Python, драйвер ODBC и SQLCMD, а затем драйвер Python для SQL Server. См. шаги в разделе 1.2, 1.3 и 2.1 в статье о [создании приложений Python с помощью SQL Server в macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Дополнительные сведения см. в разделе об [установке драйвера Microsoft ODBC в Linux и macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Установите Python и другие необходимые пакеты с помощью `sudo apt-get install python python-pip gcc g++ build-essential`. Скачайте и установите драйвер ODBC, SQLCMD и драйвер Python для SQL Server. Инструкции см. в разделе о [настройке среды разработки для разработки на Python с помощью pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Установите Python, драйвер ODBC, SQLCMD и драйвер Python для SQL Server. Инструкции см. в разделе о [настройке среды разработки для разработки на Python с помощью pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портале Azure](https://portal.azure.com/).

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

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Сведения о драйверах Microsoft Python для SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Центр по разработке на Python](https://azure.microsoft.com/develop/python/?v=17.23h)

