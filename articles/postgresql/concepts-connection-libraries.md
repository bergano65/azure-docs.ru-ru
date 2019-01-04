---
title: Библиотеки подключений для базы данных Azure для PostgreSQL
description: В этой статье описывается несколько библиотек и драйверов, которые разработчики могут использовать при написании кода приложений для подключения и запроса базы данных Azure для PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536021"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Библиотеки подключений для базы данных Azure для PostgreSQL
В этой статье перечисляются библиотеки и драйверы, которые разработчики могут использовать при разработке приложений для подключения к базе данных Azure для PostgreSQL и выполнения запросов к ней.

## <a name="client-interfaces"></a>Интерфейсы клиента
Большинство языковых клиентских библиотек для подключения к серверу PostgreSQL является внешними проектами и распространяется независимо друг от друга. Перечисленные библиотеки поддерживаются на платформах Windows, Linux и Mac для подключения к базе данных Azure для PostgreSQL. Несколько примеров быстрого запуска перечислены в разделе "Следующие шаги".

| **Язык** | **Интерфейс клиента** | **Дополнительные сведения** | **Загрузить** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Интерфейс API базы данных, совместимый с версией 2.0 | [Загрузить](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Расширение базы данных | [Установка](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Пакет pg npm](https://www.npmjs.com/package/pg) | Клиент чистого JavaScript без блокировки | [Установка](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Драйвер JDBC типа 4 | [Загрузка](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Пакет pg](https://deveiate.org/code/pg/) | Интерфейс Ruby | [Загрузить](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Пакет pq](https://godoc.org/github.com/lib/pq) | Драйвер postgres для чистого Go | [Установка](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Поставщик данных ADO.NET | [Загрузить](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Драйвер ODBC | [Загрузить](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Основной интерфейс для языка C | Включено |
| C++ | [libpqxx](http://pqxx.org/) | Новый стиль интерфейса для языка C++ | [Загрузить](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с этими краткими руководствами по подключению к базе данных Azure для PostgreSQL и выполнению запросов к ней, выбрав язык по своему усмотрению.

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
