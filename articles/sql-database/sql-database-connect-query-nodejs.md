---
title: Использование Node.js для передачи запроса в базу данных
description: Как с помощью Node.js создать программу, которая подключается к базе данных SQL Azure, и запрашивать из нее данные с использованием инструкций T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768601"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Краткое руководство. Создание запросов к базе данных SQL Azure с использованием Node.js

В этом кратком руководстве вы будете использовать Node.js для подключения к базе данных SQL Azure, а затем выполните запрос данных с помощью инструкций T-SQL.

## <a name="prerequisites"></a>предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [База данных SQL Azure](sql-database-single-database-get-started.md)
- Программное обеспечение, связанное с [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Установите Homebrew и Node.js, затем установите драйвер ODBC и SQLCMD, выполнив шаги **1.2** и **1.3** в разделе [Создание приложений Node.js с помощью SQL Server в macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Установите Node.js, затем установите драйвер ODBC и SQLCMD, выполнив шаги **1.2** и **1.3** в разделе [Создание приложений Node.js с помощью SQL Server в Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Установите Chocolatey и Node.js, затем установите драйвер ODBC и SQLCMD, выполнив шаги **1.2** и **1.3** в разделе [Создание приложений Node.js с помощью SQL Server в Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Скрипты в этой статье предназначены для использования базы данных **Adventure Works**.

> [!NOTE]
> Вы также можете использовать управляемый экземпляр SQL Azure.
>
> Для создания и настройки используйте [портал Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), или [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), затем установите подключение к [локальному серверу](sql-database-managed-instance-configure-p2s.md) или [виртуальной машине](sql-database-managed-instance-configure-vm.md).
>
> Для загрузки данных см. [Quickstart: Import a BACPAC file to a database in Azure SQL Database](sql-database-import.md) (Краткое руководство. Импорт BACPAC-файла в базу данных с помощью Azure SQL Database), а также сведения о файле [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) или [Краткое руководство. Восстановление базы данных в Управляемый экземпляр](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**. 

## <a name="create-the-project"></a>Создание проекта

Откройте командную строку и создайте папку с именем *sqltest*. Откройте созданную папку и выполните следующую команду:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Добавление кода для создания запроса к базе данных

1. Создайте файл *sqltest.js* в предпочитаемом текстовом редакторе.

1. Замените содержимое файла приведенным ниже кодом. Затем добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> В примере кода используется образец базы данных **AdventureWorksLT** для SQL Azure.

## <a name="run-the-code"></a>Выполнение кода

1. Запустите программу в командной строке.

    ```bash
    node sqltest.js
    ```

1. Убедитесь, что возвращены первые 20 строк, и закройте окно приложения.

## <a name="next-steps"></a>Дальнейшие действия

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server) (Microsoft Node.js Driver для SQL Server)

- Подключение и выполнение запроса в Windows, Linux и Mac OS с помощью [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) или [SSMS](sql-database-connect-query-ssms.md) (только в Windows)

- [Начало работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli)

- Разработка базы данных SQL Azure с помощью [.NET](sql-database-design-first-database-csharp.md) или [SSMS](sql-database-design-first-database.md)
