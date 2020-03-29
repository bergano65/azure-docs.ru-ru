---
title: Библиотеки подключения - База данных Azure для PostgreS-L - Единый сервер
description: В этой статье описано несколько библиотек и драйверов, которые можно использовать при кодировании приложений для подключения и запроса базы данных Azure для PostgreS-L - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768900"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Библиотеки подключения для базы данных Azure для PostgreS-L - Единый сервер
В этой статье перечисляются библиотеки и драйверы, которые разработчики могут использовать при разработке приложений для подключения к базе данных Azure для PostgreSQL и выполнения запросов к ней.

## <a name="client-interfaces"></a>Интерфейсы клиента
Большинство языковых клиентских библиотек для подключения к серверу PostgreSQL является внешними проектами и распространяется независимо друг от друга. Перечисленные библиотеки поддерживаются на платформах Windows, Linux и Mac для подключения к базе данных Azure для PostgreSQL. Несколько примеров быстрого запуска перечислены в разделе "Следующие шаги".

| **Язык** | **Интерфейс клиента** | **Дополнительная информация** | **Скачать** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Интерфейс API базы данных, совместимый с версией 2.0 | [Скачать](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Расширение базы данных | [Установить](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Пакет pg npm](https://www.npmjs.com/package/pg) | Клиент чистого JavaScript без блокировки | [Установить](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Драйвер JDBC типа 4 | [Скачать](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Пакет pg](https://deveiate.org/code/pg/) | Интерфейс Ruby | [Скачать](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Пакет pq](https://godoc.org/github.com/lib/pq) | Драйвер postgres для чистого Go | [Установить](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Поставщик данных ADO.NET | [Скачать](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Драйвер ODBC | [Скачать](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Основной интерфейс для языка C | Включено |
| C++ | [libpqxx](http://pqxx.org/) | Новый стиль интерфейса для языка C++ | [Скачать](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с этими краткими руководствами по подключению к базе данных Azure для PostgreSQL и выполнению запросов к ней, выбрав язык по своему усмотрению.

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (КЗ)](./connect-csharp.md) | [Перейти](./connect-go.md)
