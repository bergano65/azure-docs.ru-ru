---
title: Использование Python для создания запросов к базе данных
description: В этой статье показано, как использовать Python для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, tracking-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: bcd5a17cce9afea3325f90cb6fbc89887ada55e5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554541"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Краткое руководство. Использование Python для отправки запросов к базе данных в службе "База данных SQL Azure" или Управляемом экземпляре SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

В этом кратком руководстве вы будете использовать Python для подключения к Базе данных SQL Azure или Управляемому экземпляру SQL Azure, а затем выполните запрос данных с помощью инструкций T-SQL.

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
  |||Восстановление или импорт базы данных Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Восстановление или импорт Adventure Works из файла [BACPAC](database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 и связанное с ним программное обеспечение

  # <a name="macos"></a>[macOS](#tab/macos)

  Для установки Homebrew и Python, драйвера ODBC и SQLCMD, а также драйвера Python для SQL Server используйте шаги **1.2**, **1.3** и **2.1** в [create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) (Создание приложений Python с помощью SQL Server на MacOS).

  Дополнительные сведения см. в статье [Установка драйвера ODBC Microsoft на macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Используйте `sudo apt-get install python python-pip gcc g++ build-essential`, чтобы установить Python и другие необходимые пакеты.

  Чтобы установить драйвер ODBC, SQLCMD и драйвер Python для SQL Server, см. статью [Step 1: Configure development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) (Шаг 1. Настройка среды разработки для использования pyodbc в Python).

  Дополнительные сведения см. в статье [Установка драйвера ODBC Microsoft на Linux ](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Чтобы установить Python, драйвер ODBC, SQLCMD и драйвер Python для SQL Server, см. статью [Step 1: Configure development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) (Шаг 1. Настройка среды разработки для использования pyodbc в Python).

  Дополнительные сведения см. в статье [Драйвер ODBC Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Скрипты в этой статье предназначены для использования базы данных **Adventure Works**.

> [!NOTE]
> Вы также можете использовать Управляемый экземпляр SQL Azure.
>
> Для создания и настройки используйте [портал Azure](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) или [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), а затем установите подключение к [локальному серверу](../managed-instance/point-to-site-p2s-configure.md) или [виртуальной машине](../managed-instance/connect-vm-instance-configure.md).
>
> Для загрузки данных см. [Quickstart: Import a BACPAC file to a database in Azure SQL Database](database-import.md) (Краткое руководство. Импорт BACPAC-файла в базу данных с помощью Azure SQL Database), а также сведения о файле [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) или [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](../managed-instance/restore-sample-database-quickstart.md).

Для дальнейшего изучения Python и базы данных в службе "Базы данных SQL Azure" см. [Библиотеки Базы данных SQL Azure для Python](/python/api/overview/azure/sql), репозитория [репозиторий pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) и [выборку pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Получение сведений о подключении к серверу

Получите сведения, необходимые для подключения к базе данных в составе Базы данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для базы данных в службе "База данных SQL Azure" или полное имя сервера (или IP-адрес) рядом с полем **Узел** для Управляемого экземпляра SQL Azure или SQL Server в виртуальной машине Azure. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

> [!NOTE]
> Сведения о подключении к экземпляру SQL Server на виртуальной машине Azure см. в разделе [Подключение к экземпляру SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Создание кода для запроса базы данных 

1. Создайте файл *sqltest.py* в текстовом редакторе.  
   
1. Добавьте следующий код. Замените ваши значения на \<server>, \<database>, \<username> и \<password>.
   
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

- [Руководство по разработке первой базы данных в службе "База данных SQL Azure"](design-first-database-tutorial.md)
- [Сведения о драйверах Microsoft Python для SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Центр по разработке на Python](https://azure.microsoft.com/develop/python/?v=17.23h)

