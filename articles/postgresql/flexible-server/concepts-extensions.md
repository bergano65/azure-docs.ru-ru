---
title: Расширения — база данных Azure для PostgreSQL — гибкий сервер
description: Сведения о доступных расширениях postgres в базе данных Azure для PostgreSQL-гибкого сервера
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938519"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Расширения PostgreSQL в базе данных Azure для PostgreSQL-гибкого сервера

> [!IMPORTANT]
> Гибкий сервер Базы данных Azure для PostgreSQL предоставляется в режиме предварительной версии

PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения объединяют несколько связанных объектов SQL в один пакет, который можно загрузить или удалить из базы данных с помощью команды. После загрузки в базу данных расширения действуют как встроенные функции.

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL
Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, выполните команду " [создать расширение](https://www.postgresql.org/docs/current/sql-createextension.html) ". Эта команда загружает упакованные объекты в базу данных.

База данных Azure для PostgreSQL поддерживает подмножество расширений ключей, как указано ниже. Эти сведения также можно получить, выполнив `SHOW azure.extensions;`. Расширения, не перечисленные в этом документе, не поддерживаются в базе данных Azure для PostgreSQL-гибкого сервера. Вы не можете создать или загрузить собственное расширение в базе данных Azure для PostgreSQL.


## <a name="postgres-12-extensions"></a>Расширения postgres 12

В базе данных Azure для PostgreSQL-гибких серверов, имеющих postgres версии 12, доступны следующие расширения. 

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[амчекк](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | функции для проверки целостности отношений|
> |[раскрытия](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | метод доступа раскрытия — индекс на основе файла сигнатуры|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1,3             | Поддержка индексирования общих типов в Ло|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Поддержка индексирования общих типов в реестрах|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | тип данных для символьных строк без учета регистра|
> |[Куба](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | подключение к другим базам данных PostgreSQL из базы данных|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для целых чисел|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | шаблон словаря поиска текста для обработки расширенных синонимов|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | Вычислите большие расстояния на поверхности земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | определение сходства и расстояния между строками|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | тип данных для хранения наборов пар (ключ, значение)|
> |[интагг](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | целочисленный агрегат и перечислитель. (Устарело)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых чисел|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | тип данных для иерархических структур, схожих с деревом|
> |[пажеинспект](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | Проверка содержимого страниц базы данных на низком уровне|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1,3             | Проверка общего кэша буфера|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | изучение схемы свободного места (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | предтеплое отношение данных|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | Трассировка статистики выполнения всех выполненных инструкций SQL|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | измерение подобия текста и поиск по индексу на основе триграммов|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | Проверка сведений о видимости (на виртуальной машине) и видимости на уровне страницы|
> |[пгаудит](https://www.pgaudit.org/)                     | 1.4             | предоставляет функции аудита|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | показывать сведения о блокировке на уровне строк|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Отображение статистики на уровне кортежей|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 3.0.0           | PostGISие геометрических, географических и растровых пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | оболочка внешних данных для удаленных серверов PostgreSQL|
> |[сслинфо](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | сведения о SSL-сертификатах|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  Метод TABLESAMPLE, который принимает число строк в качестве ограничения|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  Метод TABLESAMPLE, который принимает в качестве ограничения время в миллисекундах|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | словарь поиска текста, который удаляет диакритические знаки|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | создать универсальные уникальные идентификаторы (UUID)|

## <a name="postgres-11-extensions"></a>Расширения postgres 11

Следующие расширения доступны в базе данных Azure для PostgreSQL-гибких серверов с Postgres версии 11. 

> [!div class="mx-tableFixed"]
> | **Расширение**| **версия расширения;** | **Описание** |
> |---|---|---|
> |[амчекк](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | функции для проверки целостности отношений|
> |[раскрытия](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | метод доступа раскрытия — индекс на основе файла сигнатуры|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1,3             | Поддержка индексирования общих типов в Ло|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Поддержка индексирования общих типов в реестрах|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | тип данных для символьных строк без учета регистра|
> |[Куба](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | подключение к другим базам данных PostgreSQL из базы данных|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для целых чисел|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | шаблон словаря поиска текста для обработки расширенных синонимов|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | Вычислите большие расстояния на поверхности земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | определение сходства и расстояния между строками|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | тип данных для хранения наборов пар (ключ, значение)|
> |[интагг](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | целочисленный агрегат и перечислитель. (Устарело)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых чисел|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | тип данных для иерархических структур, схожих с деревом|
> |[пажеинспект](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | Проверка содержимого страниц базы данных на низком уровне|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1,3             | Проверка общего кэша буфера|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | изучение схемы свободного места (FSM)|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | предтеплое отношение данных|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Трассировка статистики выполнения всех выполненных инструкций SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | измерение подобия текста и поиск по индексу на основе триграммов|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | Проверка сведений о видимости (на виртуальной машине) и видимости на уровне страницы|
> |[пгаудит](https://www.pgaudit.org/)                     | 1.3.1             | предоставляет функции аудита|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | показывать сведения о блокировке на уровне строк|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Отображение статистики на уровне кортежей|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgSQL|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGISие геометрических, географических и растровых пространственных типов и функций|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | оболочка внешних данных для удаленных серверов PostgreSQL|
> |[сслинфо](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | сведения о SSL-сертификатах|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | функции, управляющие целыми таблицами, включая перекрестный|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  Метод TABLESAMPLE, который принимает число строк в качестве ограничения|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  Метод TABLESAMPLE, который принимает в качестве ограничения время в миллисекундах|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | словарь поиска текста, который удаляет диакритические знаки|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | создать универсальные уникальные идентификаторы (UUID)|


## <a name="pg_prewarm"></a>pg_prewarm

Расширение pg_prewarm загружает реляционные данные в кэш. Предварительная подготовка кэшей означает, что запросы имеют лучшее время ответа при первом запуске после перезагрузки. Функция автоматического предгорячего использования в настоящее время недоступна в базе данных Azure для PostgreSQL-гибкого сервера.


## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите расширение, которое вы хотите использовать, сообщите нам. Проголосуйте за существующие запросы или создавайте новые запросы на отзыв на [форуме отзывов](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).