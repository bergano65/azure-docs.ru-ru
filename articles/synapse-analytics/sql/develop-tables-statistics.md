---
title: Создание, обновление статистики
description: Рекомендации и примеры создания и обновления статистики оптимизации запросов в синапсе SQL.
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
ms.openlocfilehash: d89baa069543c0571d42807f8034e6008eaddbc8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197599"
---
# <a name="statistics-in-synapse-sql"></a>Статистика в синапсе SQL

В этой статье приведены рекомендации и примеры создания и обновления статистики оптимизации запросов с помощью ресурсов синапсе SQL: пул SQL и SQL по запросу (Предварительная версия).

## <a name="statistics-in-sql-pool"></a>Статистика в пуле SQL

### <a name="why-use-statistics"></a>Зачем использовать статистику

Чем больше ресурс пула SQL знает о ваших данных, тем быстрее она может выполнять запросы. После загрузки данных в пул SQL сбор статистики по данным является одной из наиболее важных вещей, которые можно выполнить для оптимизации запросов.  

Оптимизатор запросов пула SQL является оптимизатором на основе затрат. В нем сравниваются затраты на различные планы запросов, а затем выбирается план с наименьшими затратами. В большинстве случаев он выбирает план, который будет выполнять самую быструю процедуру.

Например, если оптимизатор оценивает, что дата, по которой запрос фильтруется, вернет одну строку, она выберет один план. Если будет оценено, что выбранная дата возвратит 1 000 000 строк, будет возвращен другой план.

### <a name="automatic-creation-of-statistics"></a>Автоматическое создание статистики

Пул SQL анализирует входящие запросы пользователей на отсутствие статистики, если параметру AUTO_CREATE_STATISTICS базы данных присвоено значение `ON` .  Если статистика отсутствует, оптимизатор запросов создает статистику по отдельным столбцам в предикате запроса или условии объединения. Эта функция используется для улучшения оценки количества элементов для плана запроса.

> [!IMPORTANT]
> Автоматическое создание статистики в настоящее время включено по умолчанию.

Вы можете проверить, настроено ли AUTO_CREATE_STATISTICS хранилище данных, выполнив следующую команду:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Если в хранилище данных не включено AUTO_CREATE_STATISTICS, рекомендуется включить это свойство, выполнив следующую команду:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Эти инструкции активируют автоматическое создание статистики:

- SELECT
- INSERT SELECT
- CTAS
- UPDATE
- DELETE
- ОБЪЯСНИТе, когда обнаружено соединение или наличие предиката

> [!NOTE]
> Автоматическое создание статистики не создается во временных или внешних таблицах.

Автоматическое создание статистики выполняется синхронно. Поэтому, если в столбцах отсутствует статистика, может возникнуть незначительное снижение производительности запросов. Время создания статистики для одного столбца зависит от размера таблицы.

Чтобы избежать измеряемого снижения производительности, перед профилированием системы необходимо сначала создать статистику, выполнив рабочую нагрузку теста производительности.

> [!NOTE]
> Создание статистики заносится в журнал [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) в другом контексте пользователя.

При создании автоматической статистики они будут иметь форму: _WA_Sys_<8 значный идентификатор столбца в шестнадцатеричном>_<8 цифр в шестнадцатеричном>. Вы можете просмотреть уже созданную статистику, выполнив команду [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name — имя таблицы, содержащей статистические данные для вывода, которая не может быть внешней таблицей. Целевой объект — это имя целевого индекса, статистики или столбца, для которого отображаются статистические данные.

### <a name="update-statistics"></a>Обновите статистику

Лучше всего обновлять статистику в столбцах дат каждый день, когда добавляются новые даты. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Эти добавления изменяют распределение данных и делают статистику устаревшей.

Статистика по столбцу страны или региона в таблице клиентов может никогда не обновляться, поскольку распределение значений обычно не изменяется. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных.

Однако если хранилище данных содержит только одну страну или регион и вы перенесете данные из новой страны или региона, необходимо обновить статистику по столбцу страна или регион.

Ниже приведены рекомендации по обновлению статистики.

|||
|-|-|
| **Частота обновления статистики**  | Консервативная: ежедневно </br> После загрузки или преобразования данных |
| **Выборка** |  Менее 1 000 000 000 строк, используйте выборку по умолчанию (20 процентов). </br> Используя более 1 000 000 000 строк, используйте выборку из двух процентов. |

### <a name="determine-last-statistics-update"></a>Определение последнего обновления статистики

Один из первых вопросов, задаваемых при устранении неполадок запроса: **Актуальна ли статистика?**

Это не тот вопрос, на который можно ответить по возрасту данных. Актуальный объект статистики может быть старым, если исходные данные существенно не менялись. При значительном изменении числа строк или изменении материала в распределении значений для столбца наступает время обновления статистики. *then*

Отсутствует динамическое административное представление, определяющее, изменились ли данные в таблице со времени последнего обновления статистики. Знание возраста статистики может предоставить вам часть изображения. Можно использовать следующий запрос, чтобы определить время последнего обновления статистики в каждой таблице.

> [!NOTE]
> При изменении материала в распределении значений для столбца следует обновлять статистику независимо от времени последнего обновления.

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

Например, для **столбцов даты** в хранилище данных обычно требуется частое обновление статистики. При каждой загрузке строк в хранилище данных добавляются новые даты загрузки или даты транзакций. Эти добавления изменяют распределение данных и делают статистику устаревшей.

Статистика по столбцу пола в таблице клиентов может никогда не обновляться. Если предположить, что распределение между клиентами постоянно, добавление новых строк в вариант таблицы не изменит распределение данных.

Но если хранилище данных содержит только один пол, а новое требование дает несколько пола, то необходимо обновить статистику по столбцу пола. Дополнительные сведения см. в статье [статистики](/sql/relational-databases/statistics/statistics) .

### <a name="implementing-statistics-management"></a>Реализация управления статистикой

Часто рекомендуется расширять процесс загрузки данных, чтобы обеспечить обновление статистики в конце нагрузки. Загрузка данных происходит, когда таблицы наиболее часто изменяют свой размер, распределение значений или и то, и другое. Таким образом, процесс загрузки является логическим местом для реализации некоторых процессов управления.

Ниже приведены некоторые основные принципы обновления статистики в процессе загрузки:

- Убедитесь, что для каждой загружаемой таблицы обновляется по крайней мере один объект статистики. Этот процесс обновляет сведения о размере таблицы (количество строк и число страниц) в ходе обновления статистики.
- Сосредоточьтесь на столбцах, участвующих в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
- Рекомендуется чаще обновлять столбцы с возрастающим ключом, например даты транзакций, поскольку эти значения не будут включаться в гистограмму статистики.
- Рекомендуется реже обновлять столбцы со статическим распределением.
- Помните, что каждый объект статистики обновляется последовательно. Просто реализовать `UPDATE STATISTICS <TABLE_NAME>` может быть не идеальным решением, особенно для обширных таблиц с большим количеством объектов статистики.

Дополнительные сведения см. в разделе об [оценке кратности](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Примеры: создание статистики

Эти примеры показывают, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

#### <a name="create-single-column-statistics-with-default-options"></a>Создание одностолбцовой статистики с параметрами по умолчанию

Чтобы создать статистику по столбцу, введите имя для объекта статистики и имя столбца.
В этом синтаксисе все параметры используются по умолчанию. По умолчанию при создании статистики пул SQL выбирает **20 процентов** таблицы.

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

Другой вариант — указать размер выборки в процентах:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Создание одностолбцовой статистики только для некоторых строк

Можно также создать статистику для части строк в таблице, которая называется отфильтрованной статистикой.

Например, отфильтрованную статистику можно использовать при планировании запроса определенной секции большой секционированной таблицы. Создание статистики только по значениям секций позволяет улучшить точность статистики. Кроме того, вы можете улучшить производительность запросов.

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

Чтобы создать объект статистики с несколькими столбцами, используйте предыдущие примеры, но укажите больше столбцов.

> [!NOTE]
> Гистограмма, используемая для оценки количества строк в результатах запроса, доступна только для первого столбца, указанного в определении объекта статистики.

В этом примере гистограмма создана для *product\_category*. Статистика между столбцами вычисляется по * \_ категории продукта* и * \_ sub_category продукта*:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Поскольку существует корреляция между * \_ категорией продукта* и * \_ \_ подкатегорией продукта*, объект статистики с несколькими столбцами может быть полезен, если доступ к этим столбцам осуществляется одновременно.

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

В пуле SQL не предусмотрена системная хранимая процедура, эквивалентная sp_create_stats в SQL Server. Эта хранимая процедура создает объект одностолбцовой статистики для каждого столбца базы данных, для которого статистики еще нет.
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

Чтобы создать статистику по всем столбцам в таблице с использованием значений по умолчанию, выполните хранимую процедуру.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Чтобы создать статистику для всех столбцов в таблице с помощью полного сканирования, просто вызовите эту процедуру.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Чтобы создать выборочную статистику для всех столбцов в таблице, введите 3 и процент выборки. В приведенной ниже процедуре используется частота выборки в 20%.

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

Обновляя определенные объекты статистики, можно свести к минимуму затраты времени и ресурсов на управление статистикой. Это действие требует некоторого внимания для выбора наиболее подходящих объектов статистики для обновления.

#### <a name="update-all-statistics-on-a-table"></a>Обновление всей статистики для таблицы

Простой способ обновления всех объектов статистики в таблице:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Пример:

```sql
UPDATE STATISTICS dbo.table1;
```

Инструкция UPDATE STATISTICS проста в использовании. Просто помните, что он обновляет *всю* статистику для таблицы, запрашивая больше работы, чем требуется. Если производительность не является проблемой, этот метод является самым простым и наиболее полным способом гарантировать актуальность статистики.

> [!NOTE]
> При обновлении всей статистики по таблице пул SQL выполняет проверку для выборки таблицы для каждого объекта статистики. Если таблица большого размера и содержит много столбцов и статистики, может оказаться эффективнее обновлять отдельные данные статистики по необходимости.

Сведения о реализации `UPDATE STATISTICS` процедуры см. в разделе [временные таблицы](develop-tables-temporary.md). Метод реализации слегка отличается от процедуры `CREATE STATISTICS`, описанной выше, но результат одинаков.
Полный синтаксис см. в разделе [Обновление статистики](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="statistics-metadata"></a>Метаданные статистики

Существует несколько системных представлений и функций, которые можно использовать для поиска информации о статистике. Например, можно определить, что объект статистики может устареть с помощью функции STATS_DATE (). STATS_DATE () позволяет видеть время последнего создания или обновления статистики.

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

Заголовок — это метаданные статистики. Гистограмма отображает распределение значений в первом ключевом столбце объекта статистики. Вектор плотности измеряет корреляцию между столбцами. Пул SQL вычисляет оценку количества элементов с помощью любого из данных в объекте статистики.

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

`DBCC SHOW_STATISTICS()`более строго реализуется в пуле SQL по сравнению с SQL Server:

- Недокументированные функции не поддерживаются.
- Невозможно использовать Stats_stream.
- Невозможно объединить результаты для конкретных подмножеств статистических данных. Например, STAT_HEADER присоединение DENSITY_VECTOR.
- Не удается задать NO_INFOMSGS для подавления сообщений.
- Нельзя использовать квадратные скобки вокруг имен статистики.
- Невозможно использовать имена столбцов для обнаружения объектов статистики.
- Настраиваемая ошибка 2767 не поддерживается.

### <a name="next-steps"></a>Дальнейшие действия

Для дальнейшего повышения производительности запросов ознакомьтесь с разделом [Мониторинг рабочей нагрузки с помощью динамических административных представлений](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="statistics-in-sql-on-demand-preview"></a>Статистика в SQL по запросу (Предварительная версия)

Статистика создается для каждого конкретного столбца в определенном наборе данных (путь к хранилищу).

### <a name="why-use-statistics"></a>Зачем использовать статистику

Чем больше запросов SQL по запросу (Предварительная версия) известно о ваших данных, тем быстрее она может выполнять запросы к ней. Сбор статистики по данным является одним из наиболее важных вещей, которые можно выполнить для оптимизации запросов. Оптимизатор запросов по запросу SQL является оптимизатором на основе затрат. В нем сравниваются затраты на различные планы запросов, а затем выбирается план с наименьшими затратами. В большинстве случаев он выбирает план, который будет выполнять самую быструю процедуру. Например, если оптимизатор оценивает, что дата, по которой запрос фильтруется, вернет одну строку, она выберет один план. Если будет оценено, что выбранная дата возвратит 1 000 000 строк, будет возвращен другой план.

### <a name="automatic-creation-of-statistics"></a>Автоматическое создание статистики

SQL по запросу анализирует входящие запросы пользователей на отсутствие статистики. Если статистика отсутствует, оптимизатор запросов создает статистику по отдельным столбцам в предикате запроса или условии объединения, чтобы улучшить оценку количества элементов для плана запроса.

Инструкция SELECT вызывает автоматическое создание статистики.

> [!NOTE]
> Автоматическое создание статистики включено для файлов Parquet. Для CSV-файлов необходимо создать статистику вручную до тех пор, пока не будет поддерживается автоматическое создание статистических данных для файлов CSV.

Автоматическое создание статистики выполняется синхронно, поэтому при отсутствии статистики в столбцах может возникнуть незначительное снижение производительности запросов. Время создания статистики для одного столбца зависит от размера целевых файлов.

### <a name="manual-creation-of-statistics"></a>Создание статистики вручную

SQL по запросу позволяет создавать статистику вручную. Для CSV-файлов необходимо создать статистику вручную, так как автоматическое создание статистики для CSV-файлов не включено. Инструкции по созданию статистики вручную см. в приведенных ниже примерах.

### <a name="updating-statistics"></a>Обновление статистики

Изменения данных в файлах, удалении и добавлении файлов приводят к изменению распространения данных и отменяют статистику. В этом случае необходимо обновить статистику.

SQL on-Demand автоматически воссоздает статистику, если данные значительно изменяются. Каждый раз, когда статистика создается автоматически, текущее состояние набора данных также сохраняется: пути к файлам, размеры, даты последнего изменения.

Если статистика устарела, будут созданы новые шаблоны. Алгоритм проходит через данные и сравнивает их с текущим состоянием набора данных. Если размер изменений превышает заданное пороговое значение, старая статистика удаляется и будет повторно создана на основе нового набора данных.

Статистика вручную никогда не объявляется устаревшей.

> [!NOTE]
> Автоматическое воссоздание статистики включено для файлов Parquet. Для CSV-файлов необходимо удалить и создать статистику вручную до тех пор, пока не будет поддерживается автоматическое создание статистики для файлов CSV. Ознакомьтесь с приведенными ниже примерами, чтобы удалить и создать статистику.

Один из первых вопросов, задаваемых при устранении неполадок запроса: **Актуальна ли статистика?**

При значительном изменении количества строк или изменении материала в распределении значений для столбца настало время обновить статистику. *then*

> [!NOTE]
> При изменении материала в распределении значений для столбца следует обновлять статистику независимо от времени последнего обновления.

### <a name="implementing-statistics-management"></a>Реализация управления статистикой

Вам может потребоваться расширить конвейер данных, чтобы обеспечить обновление статистики при значительном изменении данных с помощью добавления, удаления или изменения файлов.

Для обновления статистики предоставляются следующие принципы:

- Убедитесь, что в наборе данных имеется по крайней мере один обновленный объект статистики. Этот размер обновления (количество строк и число страниц) обновляется в ходе обновления статистики.
- Сосредоточьтесь на столбцах, участвующих в предложениях JOIN, GROUP BY, ORDER BY и DISTINCT.
- Чаще обновляйте столбцы с возрастающим ключом, например даты транзакций, так как эти значения не будут включаться в гистограмму статистики.
- Регулярно обновляйте столбцы статического распределения.

Дополнительные сведения см. в разделе об [оценке кратности](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Примеры: создание статистики для столбца в пути OPENROWSET

В следующих примерах показано, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

> [!NOTE]
> В данный момент можно создать только одну статистику по одному столбцу.
>
> Процедура sp_create_file_statistics будет переименована в sp_create_openrowset_statistics. Роль общедоступного сервера имеет разрешение на администрирование операций с МАССОВЫми ОПЕРАЦИЯми, в то время как роль public базы данных имеет разрешения EXECUTE на sp_create_file_statistics и sp_drop_file_statistics. Это может быть изменено в будущем.

Для создания статистики используется следующая хранимая процедура:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Аргументы: [ @stmt =] N ' statement_text ' — указывает инструкцию Transact-SQL, которая будет возвращать значения столбцов, которые будут использоваться для статистики. Предложение TABLESAMPLE можно использовать для указания образцов данных для использования. Если предложение TABLESAMPLE не задано, то используется FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Выборка CSV в настоящее время не работает, для CSV поддерживается только FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Создание одностолбцовой статистики путем проверки всех строк

Чтобы создать статистику по столбцу, укажите запрос, возвращающий столбец, для которого требуется статистика.

По умолчанию, если не указано иное, SQL по запросу использует 100% данных, предоставленных в наборе данных при создании статистики.

Например, чтобы создать статистику с параметрами по умолчанию (FULLSCAN) для столбца year набора данных на основе файла Population. csv:

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

Размер выборки можно указать в процентах:

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

Чтобы обновить статистику, необходимо удалить и создать статистику. Для удаления статистики используется следующая хранимая процедура:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Процедура sp_drop_file_statistics будет переименована в sp_drop_openrowset_statistics. Роль общедоступного сервера имеет разрешение на администрирование операций с МАССОВЫми ОПЕРАЦИЯми, в то время как роль public базы данных имеет разрешения EXECUTE на sp_create_file_statistics и sp_drop_file_statistics. Это может быть изменено в будущем.

Аргументы: [ @stmt =] N ' statement_text ' — задает ту же инструкцию Transact-SQL, которая использовалась при создании статистики.

Чтобы обновить статистику для столбца year в наборе данных, основанном на CSV-файле Population, необходимо удалить и создать статистику:

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

### <a name="examples-create-statistics-for-external-table-column"></a>Примеры: создание статистики для столбца внешней таблицы

В следующих примерах показано, как использовать различные параметры для создания статистики. Параметры, которые можно использовать для каждого столбца, зависят от характеристик данных и того, как столбец будет использован в запросах.

> [!NOTE]
> В данный момент можно создать только одну статистику по одному столбцу.

Чтобы создать статистику по столбцу, введите имя для объекта статистики и имя столбца.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Аргументы: external_table указывает внешнюю таблицу, для которой должна быть создана статистика.

FULLSCAN вычисление статистики путем сканирования всех строк. FULLSCAN и SAMPLE 100 PERCENT имеют одинаковые результаты. FULLSCAN не может быть использован с параметром SAMPLE.

ВЫБОРка числа процентов указывает Приблизительный процент или количество строк в таблице или индексированном представлении, которые оптимизатор запросов будет использовать при создании статистики. Число может быть в значениях от 0 до 100.

Параметр SAMPLE нельзя использовать вместе с параметром FULLSCAN.

> [!NOTE]
> Выборка CSV в настоящее время не работает, для CSV поддерживается только FULLSCAN.

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

Чтобы обновить статистику, необходимо удалить и создать статистику. Сначала удалить статистику:

```sql
DROP STATISTICS census_external_table.sState
```

И создать статистику:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Дальнейшие действия

Дальнейшие улучшения производительности запросов см. в статье рекомендации [по использованию пула SQL](best-practices-sql-pool.md#maintain-statistics).
