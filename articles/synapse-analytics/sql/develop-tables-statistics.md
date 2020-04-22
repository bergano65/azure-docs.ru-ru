---
title: Создание, обновление статистики
description: Рекомендации и примеры для создания и обновления статистики оптимизации запросов в Synapse S'L.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5196c85ca1d68028893caee55035c6c455b37d64
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676941"
---
# <a name="statistics-in-synapse-sql"></a>Статистика по Синапсе СЗЛ

В этой статье приведены рекомендации и примеры для создания и обновления статистики оптимизации запросов с использованием ресурсов Synapse S'L: пула S'L и S'L по требованию (предварительный просмотр).

## <a name="statistics-in-sql-pool"></a>Статистика в пуле S'L

### <a name="why-use-statistics"></a>Зачем использовать статистику

Чем больше ресурс пула S'L знает о ваших данных, тем быстрее он может выполнять запросы. После загрузки данных в пул S'L сбор статистических данных является одним из наиболее важных вещей, которые можно сделать для оптимизации запросов.  

Оптимизатор запроса на пули s'L является оптимизатором затрат. Он сравнивает стоимость различных планов запросов, а затем выбирает план с наименьшими затратами. В большинстве случаев он выбирает план, который будет выполняться быстрее всего.

Например, если оптимизатор оценивает дату фильтрации запроса, он вернет одну строку, он выберет один план. Если он считает, что выбранная дата вернет 1 миллион строк, он вернет другой план.

### <a name="automatic-creation-of-statistics"></a>Автоматическое создание статистики

Пулс S'L будет анализировать входящие запросы пользователей `ON`для отсутствующих статистических данных, когда база данных AUTO_CREATE_STATISTICS опция установлена на .  Если статистика отсутствует, оптимизатор запроса создает статистику по отдельным столбикам в предикате запроса или конюшене. Эта функция используется для улучшения оценок кардинальности для плана запросов.

> [!IMPORTANT]
> Автоматическое создание статистики в настоящее время включено по умолчанию.

Вы можете проверить, AUTO_CREATE_STATISTICS ли ваш хранилище данных настроено, запустив следующую команду:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Если хранилище данных не включено AUTO_CREATE_STATISTICS, мы рекомендуем включить это свойство, запустив следующую команду:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Эти заявления будут инициировать автоматическое создание статистики:

- SELECT
- INSERT SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN при содержании соединения или наличии предиката

> [!NOTE]
> Автоматическое создание статистических данных не формируется на временных или внешних таблицах.

Автоматическое создание статистических данных осуществляется синхронно. Таким образом, при пропаже столбцов может быть несколько ухудшилась производительность запроса. Время создания статистики для одного столбца зависит от размера таблицы.

Чтобы избежать измеримой оценки снижения производительности, следует убедиться, что статистика была создана сначала путем выполнения базовой рабочей нагрузки перед профилированием системы.

> [!NOTE]
> Создание статистики регистрируется в [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) в другом контексте пользователя.

При создании автоматической статистики они принимают форму: _WA_Sys_<8-значный идентификатор столбца в Hex>_<8-значный идентификатор таблицы в Hex>. Вы можете просмотреть уже созданную статистику, запустив команду [SHOW_STATISTICS DBCC:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

В table_name — это название таблицы, содержащей отображение статистики, которая не может быть внешней таблицей. Целью является название целевого индекса, статистики или столбца, для которого отображается статистическая информация.

### <a name="update-statistics"></a>Обновите статистику

Лучше всего обновлять статистику в столбцах дат каждый день, когда добавляются новые даты. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Эти дополнения меняют распределение данных и делают статистику устаревшей.

Статистические данные о столбце страны или региона в таблице клиентов, возможно, никогда не нуждаются в обновлении, поскольку распределение значений обычно не меняется. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных.

Однако, когда ваш хранилище данных содержит только одну страну или регион, и вы приносите данные из новой страны или региона, то вам нужно обновить статистику по стране или региону столбец.

Ниже приведены рекомендации по обновлению статистики.

|||
|-|-|
| **Частота обновления статистики**  | Консервативная: ежедневно </br> После загрузки или преобразования данных |
| **Выборка** |  Менее 1 миллиарда строк используют выборку по умолчанию (20 процентов). </br> С более чем 1 миллиард строк, используйте выборку из двух процентов. |

### <a name="determine-last-statistics-update"></a>Определить последнее обновление статистики

Один из первых вопросов, задаваемых при устранении неполадок запроса: **Актуальна ли статистика?**

На этот вопрос можно ответить не по возрасту данных. Актуальный объект статистики может быть старым, если исходные данные существенно не менялись. Когда количество строк существенно изменилось или происходит существенное изменение распределения значений для столбца, *тогда* пришло время обновить статистику.

Динамическое представление управления не доступно для определения того, изменились ли данные в таблице с момента обновления статистики в последний раз. Зная возраст вашей статистики может предоставить вам часть картины. Можно использовать следующий запрос, чтобы определить время последнего обновления статистики в каждой таблице.

> [!NOTE]
> При существенном изменении распределения значений для столбца следует обновлять статистику независимо от того, когда они были обновлены в последний раз.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Например, столбцы дат** в хранилище данных обычно нуждаются в частых обновлениях статистики. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Эти дополнения меняют распределение данных и делают статистику устаревшей.

Статистические данные о гендерной колонке в таблице клиентов, возможно, никогда не нуждаются в обновлении. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных.

Но, если ваш хранилище данных содержит только один пол и новое требование приводит к нескольким полов, то вам нужно обновить статистику по гендерной колонке. Для получения дополнительной информации просмотрите статью [Статистическое управление.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Реализация управления статистикой

Часто рекомендуется расширить процесс загрузки данных, чтобы обеспечить обновление статистики в конце нагрузки. Нагрузка данных — это когда таблицы чаще всего меняют свой размер, распределение значений или и то, и другое. Таким образом, процесс загрузки является логичным местом для реализации некоторых процессов управления.

Ниже приведены некоторые основные принципы обновления статистики в процессе загрузки:

- Убедитесь, что для каждой загружаемой таблицы обновляется по крайней мере один объект статистики. Этот процесс обновляет размер таблицы (количество строк и количество страниц) информацию в рамках обновления статистики.
- Сосредоточьтесь на столбцах, участвующих в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
- Более часто следует обновлять столбцы "восходящий ключ", например даты транзакций, поскольку эти значения не будут включены в гистограмму статистики.
- Рекомендуется реже обновлять столбцы со статическим распределением.
- Помните, что каждый объект статистики обновляется последовательно. Просто реализовать `UPDATE STATISTICS <TABLE_NAME>` может быть не идеальным решением, особенно для обширных таблиц с большим количеством объектов статистики.

Дополнительные сведения см. в разделе об [оценке кратности](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Примеры: создание статистики

Эти примеры показывают, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

#### <a name="create-single-column-statistics-with-default-options"></a>Создание одностолбцовой статистики с параметрами по умолчанию

Чтобы создать статистику в столбце, укажите имя объекта статистики и название столбца.
В этом синтаксисе все параметры используются по умолчанию. По умолчанию, при создается статистика, пул аймин ьос квосам **составляет 20 процентов** таблицы.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Пример:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Создание одностолбцовой статистики путем проверки всех строк

В большинстве случаев достаточно использовать частоту выборки по умолчанию, 20 процентов. Однако вы можете настроить частоту выборки. Для выборки всей таблицы используйте следующий синтаксис:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Пример:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Создание одностолбцовой статистики с указанием размера выборки

Другой вариант, который у вас есть, это указать размер выборки в процентах:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Создание одностолбцовой статистики только для некоторых строк

Можно также создать статистику по части строк в таблице, которая называется отфильтрованную статистику.

Например, отфильтрованную статистику можно использовать при планировании запроса определенной секции большой секционированной таблицы. Создавая статистические данные только о значениях раздела, точность статистических данных повысится. Вы также будете испытывать улучшение производительности запроса.

Этот пример создает статистику по диапазону значений. Значения можно легко определить для сопоставления с диапазоном значений в секции.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Чтобы оптимизатор запросов рассмотрел возможность использования отфильтрованной статистики, когда выбирает план распределенного запроса, запрос должен быть в пределах определения объекта статистики. Если взять приведенный выше пример, предложение WHERE запроса должно указать значения col1 от 2000101 до 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Создание одностолбцовой статистики со всеми параметрами

Можно также комбинировать параметры. В приведенном ниже примере создается отфильтрованный объект статистики с настраиваемым размером выборки:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Полные справочные сведения см. в статье [CREATE STATISTICS (Transact-SQL)](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Создание многостолбцовой статистики

Чтобы создать объект статистики с несколькими столбками, используйте предыдущие примеры, но укажите больше столбцов.

> [!NOTE]
> Гистограмма, используемая для оценки количества строк в результатах запроса, доступна только для первого столбца, указанного в определении объекта статистики.

В этом примере гистограмма создана для *product\_category*. Кросс-колонки статистика рассчитывается по *категории продукта\_* и sub_category *продукта:\_*

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Поскольку существует корреляция между *категорией продукта\_* и *подкатегорией\_\_продукта,* объект статистики нескольких столбцов может быть полезен, если эти столбцы доступны одновременно.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Создание статистики для всех столбцов в таблице

Один из способов создать статистику — выполнить команды CREATE STATISTICS после создания таблицы.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Использование хранимой процедуры для создания статистики для всех столбцов в базе данных

В пуле S'L нет процедуры хранения системы, эквивалентной sp_create_stats в сервере S'L. Эта хранимая процедура создает объект одностолбцовой статистики для каждого столбца базы данных, для которого статистики еще нет.
Следующий пример поможет приступить к проектированию базы данных. Вы можете адаптировать код в соответствии со своими потребностями.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Чтобы создать статистику по всем столбцам в таблице с помощью по умолчанию, выполните сохраненную процедуру.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Чтобы создать статистику для всех столбцов в таблице с помощью полного сканирования, просто вызовите эту процедуру.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Чтобы создать выборочную статистику для всех столбцов в таблице, введите 3 и процент выборки. Процедура ниже использует 20 процентов выборки.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Примеры: обновление статистики

Чтобы обновить статистику, можно:

- Обновить один объект статистики. Указать имя объекта статистики, который вы хотите обновить.
- Обновить все объекты статистики для таблицы. Указать имя таблицы, а не один объект статистики.

#### <a name="update-one-specific-statistics-object"></a>Обновление одного указанного объекта статистики

Для обновления указанного объекта статистики используйте следующий синтаксис:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Пример:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Обновляя определенные объекты статистики, можно свести к минимуму затраты времени и ресурсов на управление статистикой. Это действие требует некоторой продумки для выбора лучших объектов статистики для обновления.

#### <a name="update-all-statistics-on-a-table"></a>Обновление всей статистики для таблицы

Простой метод обновления всех объектов статистики на столе:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Пример:

```sql
UPDATE STATISTICS dbo.table1;
```

Заявление UPDATE STATISTICS просто в использовании. Просто помните, что он обновляет *все* статистические данные на столе, вызывая больше работы, чем это необходимо. Если производительность не является проблемой, этот метод является самым простым и полным способом гарантировать, что статистика актуальна.

> [!NOTE]
> При обновлении всех статистических данных в таблице пул S'L делает сканирование для выборки таблицы для каждого объекта статистики. Если таблица большого размера и содержит много столбцов и статистики, может оказаться эффективнее обновлять отдельные данные статистики по необходимости.

Для реализации `UPDATE STATISTICS` процедуры [см.](develop-tables-temporary.md) Метод реализации слегка отличается от процедуры `CREATE STATISTICS`, описанной выше, но результат одинаков.
Для полного синтаксиса [см.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="statistics-metadata"></a>Метаданные статистики

Существует несколько системных представлений и функций, которые можно использовать для поиска информации о статистике. Например, можно увидеть, может ли объект статистики быть устаревшим, используя функцию STATS_DATE(). STATS_DATE () позволяет видеть, когда статистика была в последний раз создана или обновлена.

#### <a name="catalog-views-for-statistics"></a>Представления каталога для статистики

Вот какие системные представления показывают информацию о статистике:

| Представление каталога | Описание |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждого столбца. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждого объекта в базе данных. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждой схемы в базе данных. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждого объекта статистики. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждого столбца в объекте статистики. Ссылается на sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждой таблицы (включая внешние таблицы). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Одна строка для каждого типа данных. |

#### <a name="system-functions-for-statistics"></a>Системные функции для статистики

Эти системные функции полезны для работы со статистикой:

| Системная функция | Описание |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Дата последнего обновления объекта статистики. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Сводная и подробная информация о распределении значений согласно объекту статистики. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Сочетание столбцов и функций статистики в одном представлении

Это представление содержит столбцы, относящиеся к статистике, и результаты функции STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>Примеры DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() отображает данные, хранящиеся в объекте статистики. Эти данные состоят из трех частей:

- Заголовок
- Вектор плотности
- Гистограмма

Заголовок — это метаданные о статистике. Гистограмма отображает распределение значений в первом ключевом столбце объекта статистики. Вектор плотности измеряет корреляцию между столбцами. Пул пула S'L вычисляет оценки кардинальности с любыми данными в объекте статистики.

#### <a name="show-header-density-and-histogram"></a>Отображение заголовка, плотности и гистограммы

Этот простой пример показывает все три части объекта статистики.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Пример:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Отображение одной или нескольких частей DBCC SHOW_STATISTICS()

Если вы заинтересованы только в просмотре определенных частей, используйте предложение `WITH` и укажите, какие части требуется показать:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Пример:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Отличия DBCC SHOW_STATISTICS()

`DBCC SHOW_STATISTICS()`более строго реализована в пуле S'L по сравнению с сервером S'L:

- Недокументированные функции не поддерживаются.
- Не могу использовать Stats_stream.
- Не успокоится результаты для конкретных подмножеств статистических данных. Например, DENSITY_VECTOR join STAT_HEADER JOIN.
- NO_INFOMSGS не может быть установлен для подавления сообщений.
- Квадратные скобки вокруг имен статистики не могут быть использованы.
- Не устраняйте имена столбцов для идентификации объектов статистики.
- Пользовательская ошибка 2767 не поддерживается.

### <a name="next-steps"></a>Следующие шаги

Для дальнейшего повышения производительности запросов ознакомьтесь с разделом [Мониторинг рабочей нагрузки с помощью динамических административных представлений](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="statistics-in-sql-on-demand-preview"></a>Статистика по запросу (предварительный просмотр)

Статистика создается в определенном столбце для определенного набора данных (путь хранения).

### <a name="why-use-statistics"></a>Зачем использовать статистику

Чем больше данных (предварительный просмотр) узнает о ваших данных, тем быстрее он может выполнять запросы против него. Сбор статистических данных является одним из наиболее важных вещей, которые вы можете сделать для оптимизации запросов. Оптимизатор запросов по требованию по требованию является оптимизатором затрат. Он сравнивает стоимость различных планов запросов, а затем выбирает план с наименьшими затратами. В большинстве случаев он выбирает план, который будет выполняться быстрее всего. Например, если оптимизатор оценивает дату фильтрации запроса, он вернет одну строку, он выберет один план. Если он считает, что выбранная дата вернет 1 миллион строк, он вернет другой план.

### <a name="automatic-creation-of-statistics"></a>Автоматическое создание статистики

По требованию S'L анализирует входящие запросы пользователей на основе отсутствующей статистики. Если статистика отсутствует, оптимизатор запроса создает статистику по отдельным столбикам в предикате запроса или соединению для улучшения оценок кардинальности для плана запроса.

Заявление SELECT запустит автоматическое создание статистических данных.

> [!NOTE]
> Автоматическое создание статистики включено для файлов Паркета. Для файлов CSV необходимо создавать статистику вручную до тех пор, пока не будет поддерживаться автоматическое создание статистики файлов CSV.

Автоматическое создание статистических данных осуществляется синхронно, так что вы можете понести несколько ухудшил производительность запроса, если ваши столбцы отсутствуют статистики. Время создания статистики для одного столбца зависит от размера целевых файлов.

### <a name="manual-creation-of-statistics"></a>Ручное создание статистики

По требованию S'L позволяет создавать статистику вручную. Для файлов CSV необходимо создавать статистику вручную, поскольку автоматическое создание статистики не включено для файлов CSV. Приведенные ниже примеры инструкций о том, как вручную создавать статистику.

### <a name="updating-statistics"></a>Обновление статистики

Изменения в данных в файлах, удаляние и добавление файлов приводят к изменениям распределения данных и устаревает статистику. В этом случае необходимо обновить статистические данные.

По требованию S'L автоматически воссоздает статистику, если данные существенно изменяются. Каждый раз, когда статистика автоматически создается, сохраняется текущее состояние набора данных: пути файла, размеры, даты последней модификации.

Когда статистика устарела, будут создаваться новые. Алгоритм проходит через данные и сравнивает их с текущим состоянием набора данных. Если размер изменений превышает определенный порог, старая статистика удаляется и будет воссоздана по новому набору данных.

Ручная статистика никогда не объявляется черствым.

> [!NOTE]
> Автоматическое воссоздание статистики включено для файлов Паркета. Для файлов CSV необходимо упасть и создать статистику вручную до тех пор, пока не будет поддерживаться автоматическое создание статистики файлов CSV. Проверьте приведенные ниже примеры того, как отбросить и создать статистику.

Один из первых вопросов, задаваемых при устранении неполадок запроса: **Актуальна ли статистика?**

Когда количество строк существенно изменилось или произошло существенное изменение в распределении значений для столбца, *тогда* пришло время обновить статистику.

> [!NOTE]
> При существенном изменении распределения значений для столбца следует обновлять статистику независимо от того, когда они были обновлены в последний раз.

### <a name="implementing-statistics-management"></a>Реализация управления статистикой

Возможно, вы захотите расширить конвейер данных, чтобы обеспечить обновление статистики при значительном изменении данных путем добавления, удаления или изменения файлов.

Для обновления статистики предусмотрены следующие руководящие принципы:

- Убедитесь, что набор данных обновляется по крайней мере в одном объекте статистики. Эта информация об размере обновлений (количество строк и количество страниц) в рамках обновления статистики.
- Сосредоточьтесь на столбцах, участвующих в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
- Чаще обновляйте столбцы "восходящий ключ", например даты транзакций, поскольку эти значения не будут включены в гистограмму статистики.
- Обновляйте статические столбцы распределения реже.

Дополнительные сведения см. в разделе об [оценке кратности](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Примеры: Создание статистики для столбца в пути OPENROWSET

Следующие примеры показывают, как использовать различные варианты для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

> [!NOTE]
> Создавать статистику одной колонки можно только в этот момент.

Для создания статистики используется следующая сохраненная процедура:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Аргументы: @stmt N'statement_text" - указывается заявление О Трансакт-СЗЛ, которое вернет значения столбцов, которые будут использоваться для статистики. Можно использовать TABLESAMPLE для указания образцов данных, которые будут использоваться. Если TABLESAMPLE не указан, будет использоваться FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Выборка CSV в настоящее время не работает, только FULLSCAN поддерживается для CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Создание одностолбцовой статистики путем проверки всех строк

Чтобы создать статистику в столбце, предоставьте запрос, который возвращает столбец, для которого требуется статистика.

По умолчанию, если вы не указываете иное, S'L по требованию использует 100% данных, представленных в наборе данных, когда он создает статистику.

Например, создать статистику с опциями по умолчанию (FULLSCAN) за год столбец набора данных на основе файла population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Создание одностолбцовой статистики с указанием размера выборки

Можно указать размер выборки в процентах:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Примеры: обновление статистики

Чтобы обновить статистику, нужно упасть и создать статистику. Следующая сохраненная процедура используется для снижения статистики:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Аргументы: @stmt N'statement_text" - указывает тот же заявление О Трансакт-СЗЛ, используемое при создании статистики.

Для обновления статистики за год столбец в наборе данных, который основан на population.csv файл, необходимо отказаться и создать статистику:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Примеры: Создание статистики для внешнего столбца таблицы

Следующие примеры показывают, как использовать различные варианты для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

> [!NOTE]
> Создавать статистику одной колонки можно только в этот момент.

Чтобы создать статистику в столбце, укажите имя объекта статистики и название столбца.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Аргументы: external_table определяет внешнюю таблицу, что статистика должна быть создана.

FullSCAN вычислять статистику путем сканирования всех строк. FULLSCAN и SAMPLE 100 PERCENT имеют одинаковые результаты. FULLSCAN не может быть использован с параметром SAMPLE.

Число SAMPLE ПРОЦЕНТ определяет примерный процент или количество строк в таблице или индексируемое представление для оптимизатора запроса для использования при создаете статистику. Номер может быть от 0 до 100.

Параметр SAMPLE нельзя использовать вместе с параметром FULLSCAN.

> [!NOTE]
> Выборка CSV в настоящее время не работает, только FULLSCAN поддерживается для CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Создание одностолбцовой статистики путем проверки всех строк

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Создание одностолбцовой статистики с указанием размера выборки

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Примеры: обновление статистики

Чтобы обновить статистику, нужно упасть и создать статистику. Падение статистики в первую очередь:

```sql
DROP STATISTICS census_external_table.sState
```

И создать статистику:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Следующие шаги

Для дальнейшего улучшения производительности запроса [см.](best-practices-sql-pool.md#maintain-statistics)
