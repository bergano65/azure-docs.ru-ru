---
title: Использование Node.js для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: Как с помощью Node.js создать программу, которая подключается к базе данных SQL Azure, и запрашивать из нее данные с использованием инструкций T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 250f03809a182e541fb58f73469f46d2b281b69f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756058"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Краткое руководство. Использование Node.js для создания запросов к базе данных SQL Azure

В этой статье показано, как подключаться к базе данных SQL Azure с помощью [Node.js](https://nodejs.org). Затем можно запросить данные с использованием инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого примера понадобится следующее:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Связанное с Node.js программное обеспечение для используемой операционной системы:

  - **Mac OS.** Установите Homebrew и Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu.** Установите Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows.** Установите Chocolatey и Node.js, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Подключение к базе данных

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Необходимо настроить правила брандмауэра для общедоступного IP-адреса компьютера, на котором выполняются действия из этого руководства. Если вы используете другой компьютер или используете другой общедоступный IP-адрес, создайте [правило брандмауэра на уровне сервера на портале Azure](sql-database-server-level-firewall-rule.md).

## <a name="create-the-project"></a>Создание проекта

Откройте командную строку и создайте папку с именем *sqltest*. Перейдите к созданной папке и выполните следующую команду:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Добавление кода для создания запроса к базе данных

1. Создайте файл *sqltest.js* в предпочитаемом текстовом редакторе.

1. Замените содержимое файла приведенным ниже кодом. Затем добавьте соответствующие данные сервера, базы данных, пользователя и пароля.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
            }
        );

        request.on('row', function(columns) {
            columns.forEach(function(column) {
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

## <a name="next-steps"></a>Дополнительная информация

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server) (Microsoft Node.js Driver для SQL Server)

- Подключение и выполнение запроса в Windows, Linux и Mac OS с помощью [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) или [SSMS](sql-database-connect-query-ssms.md) (только в Windows)

- [Начало работы с .NET Core в Windows, Linux и Mac OS с помощью командной строки](/dotnet/core/tutorials/using-with-xplat-cli)

- Разработка базы данных SQL Azure с помощью [.NET](sql-database-design-first-database-csharp.md) или [SSMS](sql-database-design-first-database.md)
