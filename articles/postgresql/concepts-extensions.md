---
title: Использование расширений PostgreSQL в базе данных Azure для PostgreSQL-Single Server
description: Описывает возможность расширения функциональных возможностей базы данных с помощью расширений в базе данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998065"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Расширения PostgreSQL в базе данных Azure для PostgreSQL — один сервер
PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения объединяют несколько связанных объектов SQL в один пакет, который можно загрузить или удалить из базы данных с помощью одной команды. После загрузки в базу данных функции расширений, такие как встроенные функции.

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL
Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, выполните команду  [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html),  используя инструмент psql. При этом упакованные объекты будут загружены в базу данных.

База данных Azure для PostgreSQL поддерживает подмножество расширений ключей, как указано ниже. Расширения, выходящие за рамки перечисленных, не поддерживаются. Вы не можете создать собственное расширение в базе данных Azure для PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Расширения, поддерживаемые базой данных Azure для PostgreSQL
В таблицах ниже приведен список стандартных расширений PostgreSQL, которые в настоящее время поддерживаются базой данных Azure для PostgreSQL. Эти сведения также можно получить, выполнив `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Расширения типов данных

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Предоставляет тип данных для автоматического шифрования паролей. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Предоставляет тип строки символов без учета регистра. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Предоставляет тип данных для многомерных кубов. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Предоставляет тип данных для хранения наборов пар "ключ — значение". |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Предоставляет типы данных для международных стандартов нумерации продуктов. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Предоставляет тип данных для иерархических древовидных структур. |

### <a name="functions-extensions"></a>Расширения функций

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Предоставляет средства для вычисления ортодромических расстояний на поверхности Земли. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Предоставляет несколько функций для определения сходства и расстояния между строками. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Предоставляет функции и операторы для управления массивами целых чисел, не содержащими значений null. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Предоставляет функции шифрования. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Управляет секционированными таблицами по времени или идентификатору. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Предоставляет функции и операторы для определения сходства буквенно-цифрового текста на основе сопоставления триграмм. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Предоставляет функции для полного управления таблицами, включая перекрестные таблицы. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Создает глобально уникальные идентификаторы (UUID). (Примечание об этом расширении см. ниже.) |
> | [орафце](https://github.com/orafce/orafce) | Предоставляет подмножество функций и пакетов, эмулированных из коммерческих баз данных. |

### <a name="full-text-search-extensions"></a>Расширения для полнотекстового поиска

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Предоставляет шаблон словаря текстового поиска для целых чисел. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Словарь для текстового поиска, который удаляет из лексем знаки ударения (диакритические знаки). |

### <a name="index-types-extensions"></a>Расширения типов индекса

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Предоставляет примеры классов оператора GIN, которые реализуют поведение сбалансированного дерева для определенных типов данных. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Предоставляет классы оператора индекса GiST, которые реализуют сбалансированное дерево. |

### <a name="language-extensions"></a>Расширения языка

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Загружаемый процедурный язык PL/pgSQL. |
> | [plv8](https://plv8.github.io/) | Расширение языка Javascript для PostgreSQL, которое можно использовать для хранимых процедур, триггеров и т. д. |

### <a name="miscellaneous-extensions"></a>Прочие расширения

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Предоставляет средства для анализа того, что происходит в общем буферном кэше в режиме реального времени. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Предоставляет способ загрузки реляционных данных в буферный кэш. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. (Примечание об этом расширении см. ниже.) |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Предоставляет средства для отображения сведений о блокировке на уровне строк. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Предоставляет средства для отображения статистических данных на уровне кортежей. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Оболочка для внешних данных, используемая для доступа к данным на внешних серверах PostgreSQL. (Примечание об этом расширении см. ниже.)|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Предоставляет способ создания гипотетических индексов, которые используют ЦП или диск. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Модуль, который поддерживает подключения к другим базам данных PostgreSQL из сеанса базы данных. (Примечание об этом расширении см. ниже.) |


### <a name="postgis-extensions"></a>Расширения PostGIS

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Пространственные и географические объекты для PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Используются для анализа адреса в составных элементы. Используется для поддержки шага нормализации геокодирования адресов. |
> | [pgrouting](https://pgrouting.org/) | Расширяет геопространственную базу данных PostGIS / PostgreSQL для предоставления функции геопространственной маршрутизации. |


### <a name="time-series-extensions"></a>Расширения временных рядов

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [тимескаледб](https://docs.timescale.com/latest) | База данных SQL временных рядов, которая поддерживает автоматическое секционирование для ускорения приема и запросов. Обеспечивает ориентированные на время аналитические функции, оптимизации и масштабирование PostgreSQL для рабочих нагрузок временных рядов. Тимескаледб разрабатывается с помощью зарегистрированного товарного знака [шкалы времени, Inc.](https://www.timescale.com/) (Примечание об этом расширении см. ниже.) |

## <a name="postgres-11-extensions"></a>Расширения postgres 11

В базе данных Azure для серверов PostgreSQL доступны следующие расширения с Postgres версии 11.

> [!div class="mx-tableFixed"]
> | **Расширение**| **Версия расширения** | **Описание** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Используются для анализа адреса в составных элементы. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Пример набора данных для стандартизации адресов США|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1,3             | Поддержка индексирования общих типов в Ло|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Поддержка индексирования общих типов в реестрах|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | тип данных для символьных строк без учета регистра|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | тип данных для многомерных кубов|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | подключение к другим базам данных PostgreSQL из базы данных|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | шаблон словаря поиска текста для целых чисел|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | Вычислите большие расстояния на поверхности земли|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | определение сходства и расстояния между строками|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | тип данных для хранения наборов пар (ключ, значение)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Гипотетические индексы для PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | функции, операторы и поддержка индексов для 1-D массивов целых чисел|
> |[isn](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | типы данных для международных стандартов нумерации продуктов|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | тип данных для иерархических структур, схожих с деревом|
> |[орафце](https://github.com/orafce/orafce)                       | 3,7             | Функции и операторы, имитирующие подмножество функций и пакетов из коммерческой RDBMS|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1,3             | криптографические функции|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Расширение Пграутинг|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | показывать сведения о блокировке на уровне строк|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | Отображение статистики на уровне кортежей|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1,3             | Проверка общего кэша буфера|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Расширение для управления секционированными таблицами по времени или ИДЕНТИФИКАТОРу|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | предтеплое отношение данных|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | Трассировка статистики выполнения всех выполненных инструкций SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | измерение подобия текста и поиск по индексу на основе триграммов|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | Процедурный язык PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Доверенный процедурный язык PL/JavaScript (V8)|
> |[PostGIS](https://www.postgis.net/)                      | 2.5.1           | PostGISие геометрических, географических и растровых пространственных типов и функций|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Функции СФКГАЛ PostGIS|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger для геокодирования и инвертированного геокодирования|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Пространственные типы и функции топологии PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | оболочка внешних данных для удаленных серверов PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | функции, управляющие целыми таблицами, включая перекрестный|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | словарь поиска текста, который удаляет диакритические знаки|
> |[uuid-ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | создать универсальные уникальные идентификаторы (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Расширение pg_stat_statements предварительно загружено на каждом сервере базы данных Azure для PostgreSQL, чтобы предоставить средства отслеживания статистики выполнения инструкций SQL.
Параметр `pg_stat_statements.track`, который управляет тем, какие инструкции учитываются расширением, по умолчанию имеет значение `top`. Это означает, что все инструкции, выпущенные непосредственно клиентами, отслеживаются. Два других уровня отслеживания: `none` и `all`. Этот параметр можно настроить в качестве параметра сервера с помощью [портала Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) или [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Существует компромисс между информацией о выполнении запроса, предоставляемой pg_stat_statements, и воздействием на производительность сервера при регистрации каждой инструкции SQL. Если расширение pg_stat_statements не используется активно, рекомендуется задать для параметра `pg_stat_statements.track` значение `none`. Обратите внимание, что некоторые сторонние службы мониторинга могут зависеть от pg_stat_statements при предоставлении информации о производительности запроса, поэтому подтвердите ваш ли это случай.

## <a name="dblink-and-postgres_fdw"></a>дблинк и postgres_fdw
dblink и postgres_fdw позволяют подключаться с одного сервера PostgreSQL к другому или к другой базе данных на том же сервере. Принимающий сервер должен разрешать подключения с отправляющего сервера при помощи его брандмауэра. При использовании этих расширений для подключения между серверами Базы данных Azure для PostgreSQL, это можно сделать, задав значение "Вкл." для "Разрешить доступ к службам Azure". Это необходимо, если вы хотите использовать расширения для циклического возвращения на тот же сервер. Параметр "Разрешить доступ к службам Azure" можно найти на странице портала Azure для сервера Postgres в разделе "Безопасность подключения". Включив параметр "разрешить доступ к службам Azure", вы помещаете все IP-адреса Azure в список разрешений.

В настоящее время исходящие подключения из базы данных Azure для PostgreSQL не поддерживаются, за исключением подключений к другим серверам базы данных Azure для PostgreSQL.

## <a name="uuid"></a>uuid
Если вы планируете использовать `uuid_generate_v4()` из расширения UUID-ОССП, рассмотрите возможность сравнения с `gen_random_uuid()` из расширения пгкрипто для повышения производительности.


## <a name="timescaledb"></a>тимескаледб
Тимескаледб — это база данных временных рядов, которая упакована как расширение для PostgreSQL. Тимескаледб обеспечивает ориентированные на время аналитические функции, оптимизации и масштабирование postgres для рабочих нагрузок временных рядов.

[Узнайте больше о тимескаледб](https://docs.timescale.com/latest), охраняемом товарном знаке [шкалы времени, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Установка Тимескаледб
Чтобы установить Тимескаледб, необходимо включить его в общие библиотеки предварительной загрузки сервера. Для вступления в силу `shared_preload_libraries` изменения параметра postgres требуется перезагрузка **сервера** . Параметры можно изменить с помощью [портал Azure](howto-configure-server-parameters-using-portal.md) или [Azure CLI](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Тимескаледб можно включить в базе данных Azure для PostgreSQL версий 9,6 и 10.

Использование [портал Azure](https://portal.azure.com/):

1. Выберите сервер базы данных Azure для PostgreSQL.

2. На боковой панели выберите **Параметры сервера**.

3. Найдите параметр `shared_preload_libraries`.

4. Выберите **тимескаледб**.

5. Нажмите кнопку **сохранить** , чтобы сохранить изменения. После сохранения изменений появляется уведомление. 

6. После уведомления перезапустите сервер, чтобы применить эти изменения. Дополнительные сведения о перезапуске сервера службы "База данных Azure для PostgreSQL" см. в [этой статье](howto-restart-server-portal.md).


Теперь можно включить Тимескаледб в базе данных postgres. Подключитесь к базе данных и выполните следующую команду:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Если появится сообщение об ошибке, подтвердите [перезапуск сервера](howto-restart-server-portal.md) после сохранения shared_preload_libraries. 

Теперь можно создать таблицу Тимескаледб [с нуля](https://docs.timescale.com/getting-started/creating-hypertables) или перенести [существующие данные временных рядов в PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Следующие шаги
Если вы не видите расширение, которое вы хотите использовать, сообщите нам. Проголосуйте за существующие запросы или создавайте новые запросы на отзыв на [форуме отзывов](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
