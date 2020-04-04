---
title: Строки подключения
description: Строки подключения для бассейна Synapse S'L
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c3f73fb763fa28ac826ebb97c3c325a2408542c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633574"
---
# <a name="connection-strings-for-synapse-sql-pool"></a>Строки подключения для бассейна Synapse S'L
Вы можете подключиться к пулу S'L с несколькими различными протоколами приложений, такими как, [ADO.NET,](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx) [ODBC,](https://msdn.microsoft.com/library/jj730314.aspx) [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)и [JDBC.](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx) Ниже приведены некоторые примеры строк подключения для каждого протокола.  Кроме того, для формирования строки подключения можно использовать портал Azure.  

Чтобы создать строку подключения с помощью портала Azure, перейдите к колонке своей базы данных и в разделе *Основное* щелкните *Показать строки подключения к базам данных*.

## <a name="sample-adonet-connection-string"></a>Пример строки подключения по протоколу ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Пример строки подключения по протоколу ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Пример строки подключения по протоколу PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Пример строки подключения по протоколу JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Попробуйте установить время ожидания подключения в 300 секунд, чтобы короткие периоды недоступности были допустимы.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Чтобы начать запрос на пул S'L с помощью Visual Studio и других приложений, [см.](sql-data-warehouse-query-visual-studio.md)