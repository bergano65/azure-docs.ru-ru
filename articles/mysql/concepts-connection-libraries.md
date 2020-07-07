---
title: Библиотеки подключений — база данных Azure для MySQL
description: В этой статье приведены все библиотеки и драйверы, с помощью которых клиентские программы могут подключиться к базе данных Azure для MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79537199"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Библиотеки подключений для базы данных Azure для MySQL
В этой статье приведены все библиотеки и драйверы, с помощью которых клиентские программы могут подключиться к базе данных Azure для MySQL.

## <a name="client-interfaces"></a>Интерфейсы клиента
MySQL предлагает возможность подключения к базе данных с помощью стандартного драйвера для использования MySQL с приложениями и инструментами, совместимыми с отраслевыми стандартами ODBC и JDBC. Любая система, которая работает с ODBC или JDBC, может использовать MySQL.

| **Язык** | **Платформа** | **Дополнительный ресурс** | **Загрузить** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Собственный драйвер MySQL для PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Загрузить](https://secure.php.net/downloads.php) |
| ODBC | Платформы Windows, Linux, Mac OS X и Unix | [Руководство разработчика MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Руководство разработчика MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Не зависит от платформы | [Руководство разработчика MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Загрузить](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Руководство разработчика MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Руководство разработчика MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Руководством для разработчиков MySQL Connector/C](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Загрузить](https://dev.mysql.com/downloads/connector/c/)
| Perl | Платформы Windows, Linux, Mac OS X и Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Загрузить](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с этими краткими руководствами по подключению к базе данных Azure для MySQL и выполнению запросов к ней, выбрав язык по своему усмотрению.

[PHP](./connect-php.md)  |  [Java](./connect-java.md)  |   [.NET (C#)](./connect-csharp.md)  |  [Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Ruby](./connect-ruby.md)  |  [C++](connect-cpp.md)  |  [Go](./connect-go.md)

