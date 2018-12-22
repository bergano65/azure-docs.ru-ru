---
title: Использование PHP для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: Как с помощью PHP создать программу, которая подключается к базе данных SQL Azure, и запрашивать из нее данные с использованием инструкций T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b768b50af7ad6736e5cc3c885e6ac5016976f48a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958548"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Краткое руководство. Использование PHP для создания запросов к базе данных SQL Azure

В этой статье показано, как подключаться к базе данных SQL Azure с помощью [PHP](http://php.net/manual/en/intro-whatis.php). Затем можно запросить данные с использованием инструкций T-SQL.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого примера понадобится следующее:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра на уровне сервера](sql-database-get-started-portal-firewall.md) для общедоступного IP-адреса используемого компьютера.

- Установленное программное обеспечение, связанное с PHP, для используемой операционной системы:

    - **MacOS.** Установите, PHP, драйвер ODBC, а затем драйвер PHP для SQL Server. Сведения см. разделах [Шаг 1, 2 и 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Linux.** Установите, PHP, драйвер ODBC, а затем драйвер PHP для SQL Server. Сведения см. разделах [Шаг 1, 2 и 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Windows.** Установите PHP для IIS Express и Chocolatey, а затем драйвер ODBC и SQLCMD. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Подключение к базе данных

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Добавление кода для создания запроса к базе данных

1. Создайте файл *sqltest.php* в предпочитаемом текстовом редакторе.  

1. Замените содержимое файла приведенным ниже кодом. Затем добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Выполнение кода

1. Запустите приложение в командной строке.

   ```bash
   php sqltest.php
   ```

1. Убедитесь, что возвращены первые 20 строк, и закройте окно приложения.

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)

- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Драйверы Microsoft PHP для SQL Server)

- [Сообщите о проблемах или задайте вопросы](https://github.com/Microsoft/msphpsql/issues)

- [Шаг 4. Выполнение устойчивого подключения к SQL с помощью PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
