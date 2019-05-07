---
title: Расширения PostgreSQL в базе данных Azure для PostgreSQL - Гипермасштабируемого (Citus) (Предварительная версия)
description: Описывается, как расширить функциональные возможности базы данных с помощью расширений в базе данных Azure для PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 32870f37781b4161de692af91c79fe47efb3737e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077325"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Расширения PostgreSQL в базе данных Azure для PostgreSQL - Гипермасштабируемого (Citus) (Предварительная версия)

PostgreSQL предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения позволяют объединить несколько связанных объектов SQL в одном пакете, который затем можно загрузить или удалить из базы данных с помощью одной команды. После загрузки в базу данных расширения могут работать как встроенные функции. Дополнительные сведения о расширениях PostgreSQL см. на странице  [Упаковка связанных объектов в расширение](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Как использовать расширения PostgreSQL

Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, выполните команду  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html),  используя инструмент psql. При этом упакованные объекты будут загружены в базу данных.

В настоящее время База данных Azure для PostgreSQL поддерживает подмножество основных расширений, которые перечислены ниже. Поддерживаются только указанные выше расширения. С помощью службы базы данных Azure для PostgreSQL невозможно создать собственное расширение.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Расширения, поддерживаемые базой данных Azure для PostgreSQL

В таблицах ниже приведен список стандартных расширений PostgreSQL, которые в настоящее время поддерживаются базой данных Azure для PostgreSQL. Эти сведения также можно получить, выполнив `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Расширения типов данных

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Предоставляет тип строки символов без учета регистра. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Предоставляет тип данных для многомерных кубов. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Предоставляет тип данных для хранения наборов пар "ключ — значение". |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Предоставляет типы данных для международных стандартов нумерации продуктов. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Большой объект обслуживания. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Предоставляет тип данных для иерархических древовидных структур. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Тип данных для представления сегментов строк или с плавающей запятой интервалы. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Тип для первых n JSONB. |

### <a name="functions-extensions"></a>Расширения функций

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Функции для поля с автоматическим приращением. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Предоставляет средства для вычисления ортодромических расстояний на поверхности Земли. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Предоставляет несколько функций для определения сходства и расстояния между строками. |
> | [Вставить\_имя пользователя](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Функции для отслеживания того, кто изменил таблицу. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Целое число средства инвентаризации программного обеспечения и перечислитель (устаревший). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Предоставляет функции и операторы для управления массивами целых чисел, не содержащими значений null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Функции для отслеживания времени последнего изменения. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Предоставляет функции шифрования. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Управляет секционированными таблицами по времени или идентификатору. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Предоставляет функции и операторы для определения сходства буквенно-цифрового текста на основе сопоставления триграмм. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Функции для реализации ссылочной целостности (устаревший). |
> | сеанс\_analytics | Функции для выполнения запросов к hstore массивов. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Предоставляет функции для полного управления таблицами, включая перекрестные таблицы. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Активировать уведомления об изменениях. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Функции для реализации Переход во времени. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Создает глобально уникальные идентификаторы (UUID). |

### <a name="full-text-search-extensions"></a>Расширения для полнотекстового поиска

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Предоставляет шаблон словаря текстового поиска для целых чисел. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Текстовый шаблон словарь поиска для обработки расширенных синонима. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Словарь для текстового поиска, который удаляет из лексем знаки ударения (диакритические знаки). |

### <a name="index-types-extensions"></a>Расширения типов индекса

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [Раскрытия](https://www.postgresql.org/docs/current/bloom.html) | Метод доступа bloom — файл сигнатур на основе индекса. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Предоставляет примеры классов оператора GIN, которые реализуют поведение сбалансированного дерева для определенных типов данных. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Предоставляет классы оператора индекса GiST, которые реализуют сбалансированное дерево. |

### <a name="language-extensions"></a>Расширения языка

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Загружаемый процедурный язык PL/pgSQL. |

### <a name="hyperscale-extensions"></a>Расширения Гипермасштабируемого

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus распределенная база данных. |
> | Сегмент\_rebalancer | Безопасно перераспределить данные в группе серверов в случае добавления узла или удаления. |

### <a name="miscellaneous-extensions"></a>Прочие расширения

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [Adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Административные функции для PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Функции при проверке целостности отношения. |
> | [файл\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Оболочка для внешних данных для доступа к неструктурированного файла. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Исследование содержимого страниц базы данных на низком уровне. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Предоставляет средства для анализа того, что происходит в общем буферном кэше в режиме реального времени. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | Планировщик заданий для PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Изучите карты свободного места (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Предоставляет способ загрузки реляционных данных в буферный кэш. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. (Примечание об этом расширении см. ниже.) |
> | [PG\_видимости](https://www.postgresql.org/docs/current/pgvisibility.html) | Изучите видимости карты (VM) и сведения о видимости на уровне страницы. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Предоставляет средства для отображения сведений о блокировке на уровне строк. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Предоставляет средства для отображения статистических данных на уровне кортежей. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Оболочка для внешних данных, используемая для доступа к данным на внешних серверах PostgreSQL. (Примечание об этом расширении см. ниже.)|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Сведения о SSL-сертификаты. |
> | [TSM\_системы\_строк](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE метод, который принимает количество строк в качестве ограничения. |
> | [TSM\_системы\_времени](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE метод, который принимает время в миллисекундах как ограничение. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Предоставляет способ создания гипотетических индексов, которые используют ЦП или диск. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Модуль, который поддерживает подключения к другим базам данных PostgreSQL из сеанса базы данных. (Примечание об этом расширении см. ниже.) |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Запрос XPath и XSLT. |


### <a name="postgis-extensions"></a>Расширения PostGIS

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Пространственные и географические объекты для PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Используются для анализа адреса в составных элементы. Используется для поддержки шага нормализации геокодирования адресов. |
> | postgis\_sfcgal | Функции PostGIS SFCGAL. |
> | postgis\_tiger\_геокодирования | PostGIS tiger геокодирования и обратного геокодирования. |
> | postgis\_топологии | PostGIS топологии Пространственные типы и функции. |


## <a name="pgstatstatements"></a>pg_stat_statements
[Расширение инструкций](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) \_pg\_stat предварительно загружено на каждом сервере базы данных Azure для PostgreSQL, что предоставляет вам средства отслеживания статистики выполнения инструкций SQL.
Параметр `pg_stat_statements.track`, который управляет тем, какие инструкции учитываются расширением, по умолчанию имеет значение `top`. Это означает, что все инструкции, выпущенные непосредственно клиентами, отслеживаются. Два других уровня отслеживания: `none` и `all`. Этот параметр можно настроить в качестве параметра сервера с помощью [портала Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) или [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Существует компромисс между информацией о выполнении запроса, предоставляемой pg_stat_statements, и воздействием на производительность сервера при регистрации каждой инструкции SQL. Если расширение pg_stat_statements не используется активно, рекомендуется задать для параметра `pg_stat_statements.track` значение `none`. Обратите внимание, что некоторые сторонние службы мониторинга могут зависеть от pg_stat_statements при предоставлении информации о производительности запроса, поэтому подтвердите ваш ли это случай.

## <a name="dblink-and-postgresfdw"></a>dblink и postgres_fdw
dblink и postgres_fdw позволяют подключаться с одного сервера PostgreSQL к другому или к другой базе данных на том же сервере. Принимающий сервер должен разрешать подключения с отправляющего сервера при помощи его брандмауэра. При использовании этих расширений для подключения между серверами Базы данных Azure для PostgreSQL, это можно сделать, задав значение "Вкл." для "Разрешить доступ к службам Azure". Это необходимо, если вы хотите использовать расширения для циклического возвращения на тот же сервер. Параметр "Разрешить доступ к службам Azure" можно найти на странице портала Azure для сервера Postgres в разделе "Безопасность подключения". Значение "Вкл." для "Разрешить доступ к службам Azure" добавляет в список разрешений все IP-адреса Azure.

В настоящее время исходящие подключения из базы данных Azure для PostgreSQL не поддерживаются, за исключением подключений с другой базой данных Azure для PostgreSQL серверов.
