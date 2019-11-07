---
title: Подключение к хранилищу данных SQL Azure
description: Подключитесь к хранилищу данных SQL Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a14b99753c9f06f2e0cf32dd8b5c7776cfdad89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685876"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Подключение к хранилищу данных SQL Azure
Подключитесь к хранилищу данных SQL Azure.

## <a name="find-your-server-name"></a>Поиск имени сервера
Имя сервера в следующем примере — samplesvr.database.windows.net. Чтобы найти полное имя сервера, сделайте следующее.

1. Перейдите на [портал Azure][Azure portal].
2. Щелкните **Хранилища данных SQL**.
3. Щелкните хранилище данных, к которому вы хотите подключиться.
4. Найдите полное имя сервера.
   
    ![Полное имя сервера][1]

## <a name="supported-drivers-and-connection-strings"></a>Поддерживаемые драйверы и строки подключения
Хранилище данных SQL Azure поддерживает [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP]и [JDBC][JDBC]. Щелкните один из указанных типов драйверов для получения информации об обновлениях и документации. Чтобы автоматически создать строку подключения используемого драйвера на портале Azure, щелкните **Показать строки подключения к базам данных** на странице из предыдущего примера. Ниже приведены примеры синтаксиса строк подключения для каждого драйвера.

> [!NOTE]
> Рекомендуем задать время ожидания подключения, равное 300 секундам, чтобы подключение могло выдерживать короткие периоды недоступности.
> 
> 

### <a name="adonet-connection-string-example"></a>Пример строки подключения ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Пример строки подключения ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Пример строки подключения PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Пример строки подключения JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Параметры подключения
Хранилище данных SQL стандартизирует некоторые параметры при установке подключения и создании объектов. Такие параметры нельзя переопределить. К ним относятся следующие:

| Параметр базы данных | Значение |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ВКЛ |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ВКЛ |
| [DATEFORMAT][DATEFORMAT] |мдг |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Дальнейшие действия
Сведения о подключении и выполнении запросов с помощью Visual Studio см. в разделе [запросы с помощью Visual Studio][Query with Visual Studio]. Дополнительные сведения о параметрах проверки подлинности см. в статье [Проверка подлинности в хранилище данных SQL Azure][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


