---
title: Расширения - База данных Azure для PostgreS'L - Единый сервер
description: Узнайте о доступных расширениях Postgres в базе данных Azure для PostgreS-L - Единый сервер
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201277"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Расширение PostgreS'L в базе данных Azure для PostgreS'L - Единый сервер
PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения объединяют несколько связанных объектов СЗЛ в один пакет, который может быть загружен или удален из базы данных с помощью одной команды. После загрузки в базе данных расширения функционируют как встроенные функции.

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL
Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, запустите команду [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) с помощью psql-инструмента для загрузки упакованных объектов в базу данных.

База данных Azure для PostgreS'L поддерживает подмножество ключевых расширений, перечисленных ниже. Эти сведения также можно получить, выполнив `SELECT * FROM pg_available_extensions;`. Расширения, выходящие за рамки перечисленных, не поддерживаются. Вы не можете создать собственное расширение в базе данных Azure для PostgreS'L.

## <a name="postgres-11-extensions"></a>Postgres 11 расширений

Следующие расширения доступны в базе данных Azure для серверов PostgreS'L, которые имеют версию Postgres 11. 

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Используются для анализа адреса в составных элементы. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Пример набора данных Address Standardizer В США|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1,3             | поддержка индексации общих типов данных в GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | поддержка индексации общих типов данных в GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | тип данных для строк, не чувствительных к случаям, символов|
> |[Куба](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | подключиться к другим базам данных PostgreS'L из базы данных|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для integers|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | вычислить большие расстояния круга на поверхности Земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | определить сходство и расстояние между строками|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | тип данных для хранения наборов (ключевых, значение) пар|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Гипотетические индексы для PostgreS'L|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых рядов|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | тип данных для иерархических древоподобных структур|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Функции и операторы, которые эмулируют подмножество функций и пакетов из коммерческих RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | обеспечивает аудиторскую функциональность|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting Расширение|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | показать информацию о блокировке уровня строки|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | показать статистику уровня tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1,3             | изучить общий буферный кэш|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Расширение для управления разделенными таблицами по времени или идентификатору|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | предтепловые данные отношений|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | отслеживать статистику выполнения всех выполненных отчетов S'L|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | измерение сходства текста и поиск индекса на основе триграмм|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgS'L|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) доверенный процедурный язык|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Геометрия, география и пространственные типы и функции PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Функции PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Почтовый геокодер тигра PostGIS и обратный геокодер|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | ПостГИС топологии пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | обертка для удаленных серверов PostgreS'L|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | функции, которые манипулируют целыми таблицами, включая перекрестный|
> |[временной шкалы](https://docs.timescale.com/latest)                    | 1.3.2             | Позволяет масштабируемые вставки и сложные запросы для данных временных рядов|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | словарь поиска текста, который удаляет акценты|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | генерировать универсально уникальные идентификаторы (UUID)|

## <a name="postgres-10-extensions"></a>Postgres 10 расширений 

Следующие расширения доступны в базе данных Azure для серверов PostgreS'L, которые имеют версию Postgres 10.

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Используются для анализа адреса в составных элементы. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Пример набора данных Address Standardizer В США|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1,3             | поддержка индексации общих типов данных в GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | поддержка индексации общих типов данных в GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | тип данных для автоматически хихиковых паролей|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | тип данных для строк, не чувствительных к случаям, символов|
> |[Куба](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | подключиться к другим базам данных PostgreS'L из базы данных|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для integers|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1,1             | вычислить большие расстояния круга на поверхности Земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1,1             | определить сходство и расстояние между строками|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | тип данных для хранения наборов (ключевых, значение) пар|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Гипотетические индексы для PostgreS'L|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых рядов|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1,1             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1,1             | тип данных для иерархических древоподобных структур|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Функции и операторы, которые эмулируют подмножество функций и пакетов из коммерческих RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | обеспечивает аудиторскую функциональность|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting Расширение|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | показать информацию о блокировке уровня строки|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | показать статистику уровня tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1,3             | изучить общий буферный кэш|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Расширение для управления разделенными таблицами по времени или идентификатору|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1,1             | предтепловые данные отношений|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | отслеживать статистику выполнения всех выполненных отчетов S'L|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1,3             | измерение сходства текста и поиск индекса на основе триграмм|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgS'L|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) доверенный процедурный язык|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Геометрия, география и пространственные типы и функции PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Функции PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Почтовый геокодер тигра PostGIS и обратный геокодер|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | ПостГИС топологии пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | обертка для удаленных серверов PostgreS'L|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | функции, которые манипулируют целыми таблицами, включая перекрестный|
> |[временной шкалы](https://docs.timescale.com/latest)                    | 1.1.1             | Позволяет масштабируемые вставки и сложные запросы для данных временных рядов|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1,1             | словарь поиска текста, который удаляет акценты|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1,1             | генерировать универсально уникальные идентификаторы (UUID)|

## <a name="postgres-96-extensions"></a>Postgres 9.6 расширения 

Следующие расширения доступны в базе данных Azure для серверов PostgreS'L, которые имеют версию Postgres 9.6.

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Используются для анализа адреса в составных элементы. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Пример набора данных Address Standardizer В США|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | поддержка индексации общих типов данных в GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | поддержка индексации общих типов данных в GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | тип данных для автоматически хихиковых паролей|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1,3             | тип данных для строк, не чувствительных к случаям, символов|
> |[Куба](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | подключиться к другим базам данных PostgreS'L из базы данных|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для integers|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1,1             | вычислить большие расстояния круга на поверхности Земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1,1             | определить сходство и расстояние между строками|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | тип данных для хранения наборов (ключевых, значение) пар|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Гипотетические индексы для PostgreS'L|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых рядов|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1,1             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1,1             | тип данных для иерархических древоподобных структур|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Функции и операторы, которые эмулируют подмножество функций и пакетов из коммерческих RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | обеспечивает аудиторскую функциональность|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting Расширение|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | показать информацию о блокировке уровня строки|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | показать статистику уровня tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | изучить общий буферный кэш|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Расширение для управления разделенными таблицами по времени или идентификатору|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1,1             | предтепловые данные отношений|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | отслеживать статистику выполнения всех выполненных отчетов S'L|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1,3             | измерение сходства текста и поиск индекса на основе триграмм|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgS'L|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) доверенный процедурный язык|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Геометрия, география и пространственные типы и функции PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Функции PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Почтовый геокодер тигра PostGIS и обратный геокодер|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | ПостГИС топологии пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | обертка для удаленных серверов PostgreS'L|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | функции, которые манипулируют целыми таблицами, включая перекрестный|
> |[временной шкалы](https://docs.timescale.com/latest)                    | 1.1.1             | Позволяет масштабируемые вставки и сложные запросы для данных временных рядов|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1,1             | словарь поиска текста, который удаляет акценты|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1,1             | генерировать универсально уникальные идентификаторы (UUID)|

## <a name="postgres-95-extensions"></a>Postgres 9.5 расширений 

Следующие расширения доступны в базе данных Azure для серверов PostgreS'L, которые имеют версию Postgres 9.5.

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Используются для анализа адреса в составных элементы. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Пример набора данных Address Standardizer В США|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | поддержка индексации общих типов данных в GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1,1             | поддержка индексации общих типов данных в GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | тип данных для автоматически хихиковых паролей|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1,1             | тип данных для строк, не чувствительных к случаям, символов|
> |[Куба](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1,1             | подключиться к другим базам данных PostgreS'L из базы данных|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для integers|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | вычислить большие расстояния круга на поверхности Земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | определить сходство и расстояние между строками|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1,3             | тип данных для хранения наборов (ключевых, значение) пар|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Гипотетические индексы для PostgreS'L|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | функции, операторы и поддержка индексов для 1-D массивов целых рядов|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | тип данных для иерархических древоподобных структур|
> |[orafce](https://github.com/orafce/orafce)                       | 3,7             | Функции и операторы, которые эмулируют подмножество функций и пакетов из коммерческих RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | обеспечивает аудиторскую функциональность|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | криптографические функции|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting Расширение|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1,1             | показать информацию о блокировке уровня строки|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1,3             | показать статистику уровня tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1,1             | изучить общий буферный кэш|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Расширение для управления разделенными таблицами по времени или идентификатору|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | предтепловые данные отношений|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1,3             | отслеживать статистику выполнения всех выполненных отчетов S'L|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1,1             | измерение сходства текста и поиск индекса на основе триграмм|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgS'L|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Геометрия, география и пространственные типы и функции PostGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Функции PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Почтовый геокодер тигра PostGIS и обратный геокодер|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | ПостГИС топологии пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | обертка для удаленных серверов PostgreS'L|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | функции, которые манипулируют целыми таблицами, включая перекрестный|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | словарь поиска текста, который удаляет акценты|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | генерировать универсально уникальные идентификаторы (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Расширение pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) предварительно загружается на каждую базу данных Azure для сервера PostgreS'L, чтобы предоставить вам средства отслеживания статистики выполнения заявлений S'L.
Параметр `pg_stat_statements.track`, который управляет тем, какие инструкции учитываются расширением, по умолчанию имеет значение `top`. Это означает, что все инструкции, выпущенные непосредственно клиентами, отслеживаются. Два других уровня отслеживания: `none` и `all`. Этот параметр можно настроить в качестве параметра сервера с помощью [портала Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) или [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Существует компромисс между информацией о выполнении запроса, предоставляемой pg_stat_statements, и воздействием на производительность сервера при регистрации каждой инструкции SQL. Если расширение pg_stat_statements не используется активно, рекомендуется задать для параметра `pg_stat_statements.track` значение `none`. Обратите внимание, что некоторые сторонние службы мониторинга могут зависеть от pg_stat_statements при предоставлении информации о производительности запроса, поэтому подтвердите ваш ли это случай.

## <a name="dblink-and-postgres_fdw"></a>хихика тьмоха и postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) и [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) позволяют подключиться с одного сервера PostgreS'L на другой или в другую базу данных на том же сервере. Принимающий сервер должен разрешать подключения с отправляющего сервера при помощи его брандмауэра. При использовании этих расширений для подключения между серверами Базы данных Azure для PostgreSQL, это можно сделать, задав значение "Вкл." для "Разрешить доступ к службам Azure". Это необходимо, если вы хотите использовать расширения для циклического возвращения на тот же сервер. Параметр "Разрешить доступ к службам Azure" можно найти на странице портала Azure для сервера Postgres в разделе "Безопасность подключения". Включение "Разрешить доступ к службам Azure" ON помещает все iPs Azure в список разрешений.

В настоящее время исходящие соединения из базы данных Azure для PostgreS'L не поддерживаются, за исключением подключения к другим базам данных Azure для серверов PostgreS-L.

## <a name="uuid"></a>uuid
Если вы планируете `uuid_generate_v4()` использовать из [расширения uuid-ossp,](https://www.postgresql.org/docs/current/uuid-ossp.html)подумайте о сравнении с `gen_random_uuid()` расширением [pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) для повышения производительности.

## <a name="pgaudit"></a>pgAudit
[Расширение pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) обеспечивает журнал аудита сеансов и объектов. Чтобы узнать, как использовать это расширение в базе данных Azure для PostgreS'L, посетите [статью о концепциях аудита](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Расширение pg_prewarm загружает реляционные данные в кэш. Prewarming ваши кэши означает, что ваши запросы имеют лучшее время отклика на первом запуске после перезагрузки. В Postgres 10 и ниже, предварительное потепление осуществляется вручную с помощью [претеплой функции.](https://www.postgresql.org/docs/10/pgprewarm.html)

В Postgres 11 и выше, вы можете настроить предварительное потепление произойдет [автоматически.](https://www.postgresql.org/docs/current/pgprewarm.html) Необходимо включить pg_prewarm в `shared_preload_libraries` список параметра и перезапустить сервер для применения изменения. Параметры можно установить на [портале Azure,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API или шаблоне ARM. 

## <a name="timescaledb"></a>СрокиDB
TimescaleDB — это база данных временных рядов, которая упакована в качестве расширения для PostgreS'L. TimescaleDB предоставляет аналитические функции, оптимизацию и масштабирует Postgres для рабочих нагрузок временных рядов.

[Узнайте больше о TimescaleDB](https://docs.timescale.com/latest), зарегистрированной торговой маркой [Timescale, Inc. .](https://www.timescale.com/) База данных Azure для PostgreS'L предоставляет версию шкалы времени с открытым исходным кодом. Чтобы узнать, какие функции Шкалы [the Timescale product comparison](https://www.timescale.com/products/)Времени доступны в этой версии, см.

### <a name="installing-timescaledb"></a>Установка TimescaleDB
Чтобы установить TimescaleDB, необходимо включить его в общие библиотеки предварительной загрузки сервера. Изменение параметра Postgres `shared_preload_libraries` требует **перезагрузки сервера,** чтобы ввести в действие. Вы можете изменить параметры с помощью [портала Azure](howto-configure-server-parameters-using-portal.md) или [Azure CLI.](howto-configure-server-parameters-using-cli.md)

Использование [портала Azure:](https://portal.azure.com/)

1. Выберите сервер базы данных Azure для PostgreSQL.

2. На боковой панели выберите **параметры сервера.**

3. Найдите параметр `shared_preload_libraries`.

4. Выберите **TimescaleDB**.

5. Выберите **Сохранить,** чтобы сохранить свои изменения. Вы получаете уведомление после сохранения изменения. 

6. После уведомления **перезапустите** сервер, чтобы применить эти изменения. Дополнительные сведения о перезапуске сервера службы "База данных Azure для PostgreSQL" см. в [этой статье](howto-restart-server-portal.md).


Теперь можно включить TimescaleDB в базу данных Postgres. Подключитесь к базе данных и выпустите следующую команду:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Если вы видите ошибку, подтвердите, что вы [перезапустили сервер](howto-restart-server-portal.md) после сохранения shared_preload_libraries. 

Теперь можно создавать гипертаблицу TimescaleDB [с нуля](https://docs.timescale.com/getting-started/creating-hypertables) или мигрировать [существующие данные временных рядов в PostgreS'L.](https://docs.timescale.com/getting-started/migrating-data)

### <a name="restoring-a-timescale-database"></a>Восстановление базы данных шкалы времени
Для восстановления базы данных Шкала времени с помощью pg_dump и `timescaledb_pre_restore()` pg_restore `timescaledb_post restore()`необходимо запустить две процедуры помощника в базе данных назначения: и .

Сначала подготовьте базу данных назначения:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Теперь вы можете запустить pg_dump исходной базе данных, а затем сделать pg_restore. После восстановления обязательно запустите следующую команду в восстановленной базе данных:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Дальнейшие действия
Если вы не видите расширение, которое вы хотите использовать, сообщите нам. Проголосуйте за существующие запросы или создайте новые запросы обратной связи на нашем [форуме обратной связи.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
