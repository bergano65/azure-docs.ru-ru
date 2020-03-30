---
title: Расширения - Гипермасштаб (Citus) - База данных Azure для PostgreS'L
description: Описывает возможность расширения функциональности базы данных с помощью расширений в базе данных Azure для PostgreS-L - Hyperscale (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485409"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Расширения PostgreS'L в базе данных Azure для PostgreS-L - Гипермасштаб (Citus)

PostgreS'L предоставляет возможность расширить функциональность базы данных с помощью расширений. Расширения позволяют объединить несколько связанных объектов SQL в одном пакете, который затем можно загрузить или удалить из базы данных с помощью одной команды. После загрузки в базе данных расширения могут функционировать как встроенные функции. Для получения дополнительной информации о расширениях PostgreS'L [см.](https://www.postgresql.org/docs/current/static/extend-extensions.html)

## <a name="use-postgresql-extensions"></a>Используйте расширения PostgreS'L

Чтобы использовать расширения PostgreSQL, их необходимо сначала установить в базу данных. Чтобы установить определенное расширение, запустите команду [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) с инструмента psql для загрузки упакованных объектов в базу данных.

База данных Azure для PostgreS'L - Hyperscale (Citus) в настоящее время поддерживает подмножество ключевых расширений, перечисленных здесь. Расширения, кроме перечисленных, не поддерживаются. Вы не можете создать свое собственное расширение с базой данных Azure для PostgreS'L.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Расширения, поддерживаемые базой данных Azure для PostgreSQL

В таблицах ниже приведен список стандартных расширений PostgreSQL, которые в настоящее время поддерживаются базой данных Azure для PostgreSQL. Эти сведения также можно получить, выполнив `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Расширения типов данных

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Предоставляет тип строки символов без учета регистра. |
> | [Куба](https://www.postgresql.org/docs/current/static/cube.html) | Предоставляет тип данных для многомерных кубов. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Обеспечивает тип данных для хранения наборов пар ключей. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Обеспечивает структуру данных HyperLogLog. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Предоставляет типы данных для международных стандартов нумерации продуктов. |
> | [Ло](https://www.postgresql.org/docs/current/lo.html) | Обслуживание больших объектов. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Предоставляет тип данных для иерархических древовидных структур. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | Тип данных для представления сегментов линий или интервалов плавающей точки. |
> | [топн](https://github.com/citusdata/postgresql-topn/) | Тип для топ-н JSONB. |

### <a name="full-text-search-extensions"></a>Расширения для полнотекстового поиска

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Предоставляет шаблон словаря текстового поиска для целых чисел. |
> | [диктант\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Шаблон словаря текстового поиска для расширенной обработки синонимов. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Словарь для текстового поиска, который удаляет из лексем знаки ударения (диакритические знаки). |

### <a name="functions-extensions"></a>Расширения функций

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [автоцинк](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Функции для аутоинкреминирующих полей. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Предоставляет средства для вычисления ортодромических расстояний на поверхности Земли. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Предоставляет несколько функций для определения сходства и расстояния между строками. |
> | [вставить\_имя пользователя](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Функции для отслеживания того, кто изменил таблицу. |
> | [в понесе](https://www.postgresql.org/docs/current/intagg.html) | Агрегатор и регистратор Integer (устаревший). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Предоставляет функции и операторы для управления массивами целых чисел, не содержащими значений null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Функции для отслеживания последнего времени модификации. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Предоставляет функции шифрования. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Управляет секционированными таблицами по времени или идентификатору. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Предоставляет функции и операторы для определения сходства буквенно-цифрового текста на основе сопоставления триграмм. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Функции для реализации референтной целостности (устаревшие). |
> | сессионная\_аналитика | Функции для запроса массивов hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Предоставляет функции для полного управления таблицами, включая перекрестные таблицы. |
> | [Tcn](https://www.postgresql.org/docs/current/tcn.html) | Инициированные уведомления об изменении. |
> | [путешествие во времени](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Функции для реализации путешествий во времени. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Создает глобально уникальные идентификаторы (UUID). |

### <a name="hyperscale-extensions"></a>Гипермасштабные расширения

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [цитус](https://github.com/citusdata/citus) | Цитус распределенная база данных. |
> | осколок\_ребалансизатор | Безопасно перебалансировать данные в группе сервера в случае добавления или удаления узлов. |

### <a name="index-types-extensions"></a>Расширения типов индексов

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [Цветения](https://www.postgresql.org/docs/current/bloom.html) | Метод доступа Bloom - индекс на основе файла на основе подписи. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Предоставляет выборочные классы операторов GIN, которые реализуют поведение типа B-tree для определенных типов данных. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Предоставляет классы оператора индекса GiST, которые реализуют сбалансированное дерево. |

### <a name="language-extensions"></a>Расширения языка

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Загружаемый процедурный язык PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Прочие расширения

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [админпак](https://www.postgresql.org/docs/current/adminpack.html) | Административные функции для PostgreS'L. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Функции для проверки целостности отношений. |
> | [файл\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Обертка для получения фиксированного файла для иностранных данных. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Проверяйте содержимое страниц баз данных на низком уровне. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Предоставляет средства для анализа того, что происходит в общем буферном кэше в режиме реального времени. |
> | [пг\_cron](https://github.com/citusdata/pg_cron) | Планировщик работы для PostgreS'L. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Изучите карту свободного пространства (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Предоставляет способ загрузки реляционных данных в буферный кэш. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Предоставляет средства для отслеживания статистики выполнения всех инструкций SQL, выполняемых сервером. Подробнее об этом продлении читайте в разделе "pg_stat_statements". |
> | [pg\_видимость](https://www.postgresql.org/docs/current/pgvisibility.html) | Изучите карту видимости (VM) и информацию о видимости на уровне страниц. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Предоставляет средства для отображения сведений о блокировке на уровне строк. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Предоставляет средства для отображения статистических данных на уровне кортежей. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Оболочка для внешних данных, используемая для доступа к данным на внешних серверах PostgreSQL. Подробнее об этом продлении читайте в разделе "и postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Информация о Сертификатах SSL. |
> | [tsm\_\_системные строки](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Метод TABLESAMPLE, который принимает количество строк в качестве лимита. |
> | [tsm\_\_системное время](https://www.postgresql.org/docs/current/tsm-system-time.html) | Метод TABLESAMPLE, который принимает время в миллисекундах как предел. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Предоставляет способ создания гипотетических индексов, которые используют ЦП или диск. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Модуль, который поддерживает подключения к другим базам данных PostgreSQL из сеанса базы данных. Подробнее об этом продлении читайте в разделе "и postgres_fdw". |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Запрос XPath и XSLT. |


### <a name="postgis-extensions"></a>Расширения PostGIS

> [!div class="mx-tableFixed"]
> | **Расширение** | **Описание** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Пространственные и географические объекты для PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Используются для анализа адреса в составных элементы. Используется для поддержки шага нормализации геокодирования адресов. |
> | postgis\_sfcgal | Функции PostGIS SFCGAL. |
> | \_постгис\_тигр геокодер | Почтовый тигровый геокодер и обратный геокодер. |
> | постгис\_топология | PostGIS топологии пространственных типов и функций. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Расширение\_pg\_stat-инструкций](https://www.postgresql.org/docs/current/pgstatstatements.html) предварительно загружается на каждую базу данных Azure для сервера PostgreS'L, чтобы предоставить вам средства отслеживания статистики выполнения заявлений S'L.

Настройка `pg_stat_statements.track` контролирует, какие операторы учитываются расширением. Он по `top`умолчанию, что означает, что все заявления, выданные непосредственно клиентами отслеживаются. Два других уровня отслеживания: `none` и `all`. Этот параметр можно настроить в качестве параметра сервера с помощью [портала Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) или [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Существует компромисс между информацией о выполнении запроса pg_stat_statements и влиянием на производительность сервера, когда он регистрирует каждую выписку S'L. Если вы не активно используете расширение pg_stat_statements, `pg_stat_statements.track` мы `none`рекомендуем вам установить. Некоторые сторонние службы мониторинга могут полагаться на pg_stat_statements для предоставления информации о производительности запроса, поэтому подтвердите, действительно ли это относится к вам или нет.

## <a name="dblink-and-postgres_fdw"></a>хихика тьмоха и postgres_fdw
Вы можете использовать dblink и postgres_fdw для подключения с одного сервера PostgreS'L на другой или в другую базу данных на том же сервере. Принимающий сервер должен разрешать подключения с отправляющего сервера при помощи его брандмауэра. Чтобы использовать эти расширения для подключения между базой данных Azure для серверов PostgreS'L, **установите разрешить доступ к службам Azure** в ON. Вам также нужно повернуть эту настройку ON, если вы хотите использовать расширения для цикла обратно на тот же сервер. **Параметры услуг Azure** можно найти на странице портала Azure для сервера Postgres под **системой безопасности подключения.** Включение **разрешить доступ к службам Azure** в белых списках всех испытое Azure.

В настоящее время исходящие соединения из базы данных Azure для PostgreS'L не поддерживаются, за исключением подключения к другим базам данных Azure для серверов PostgreS-L.
