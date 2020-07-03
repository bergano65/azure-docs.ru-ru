---
title: Временные таблицы
description: Основные рекомендации по использованию временных таблиц в пуле синапсе SQL, в которых выделяются принципы временных таблиц уровня сеанса.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408041"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Временные таблицы в пуле SQL синапсе
В этой статье содержатся важные рекомендации по использованию временных таблиц и приводятся основные концепции временных таблиц уровня сеанса. 

Сведения, приведенные в этой статье, помогут вам разделения код, улучшая как многократное использование, так и простоту обслуживания.

## <a name="what-are-temporary-tables"></a>Что собой представляют временные таблицы
Временные таблицы полезны при обработке данных, особенно во время преобразования, когда промежуточные результаты являются временными. В пуле SQL временные таблицы существуют на уровне сеанса.  

Временные таблицы видны только сеансу, в котором они были созданы, и автоматически удаляются при выходе из сеанса.  

Временные таблицы позволяют оптимизировать производительность, так как их результаты записываются в локальное, а не удаленное хранилище.

Временные таблицы полезны при обработке данных, особенно во время преобразования, когда промежуточные результаты являются временными. В SQL Analytics временные таблицы существуют на уровне сеанса.  Они видны только сеансу, в котором они были созданы. Таким образом, они автоматически удаляются при выходе из сеанса. 

## <a name="temporary-tables-in-sql-pool"></a>Временные таблицы в пуле SQL

В ресурсе пула SQL временные таблицы обеспечивают выигрыш в производительности, поскольку их результаты записываются в локальную, а не в удаленное хранилище.

### <a name="create-a-temporary-table"></a>Создание временной таблицы

Для создания временной таблицы к имени таблицы добавляется префикс `#`.  Пример:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Временные таблицы можно также создать с помощью `CTAS` точно таким же образом:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` — мощная команда, которая особенно эффективна при использовании пространства журнала транзакций. 
> 
> 

## <a name="dropping-temporary-tables"></a>Удаление временных таблиц
При создании сеанса не должно быть ни одной временной таблицы.  

При вызове той же хранимой процедуры, которая создает временную с тем же именем, чтобы гарантировать успешность выполнения `CREATE TABLE` инструкций, можно использовать простую проверку готовности с помощью `DROP` , как показано в следующем примере:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Для обеспечения согласованности кода рекомендуется использовать этот шаблон как для таблиц, так и для временных таблиц.  Также рекомендуется использовать `DROP TABLE` для удаления временных таблиц после завершения работы с ними в коде.  

В процессе разработки хранимых процедур команды DROP можно увидеть в конце процедуры, чтобы убедиться, что эти объекты очищены.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Разделение кода на модули
Так как временные таблицы можно просматривать в любом месте пользовательского сеанса, эту возможность можно использовать для разделения кода приложения.  

Например, следующая хранимая процедура создает DDL для обновления всей статистики в базе данных по имени статистики:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

На этом этапе единственное произошедшее действие — создание хранимой процедуры, которая создает временную таблицу, #stats_ddl с помощью инструкций DDL.  

Эта хранимая процедура удаляет существующий #stats_ddl, чтобы убедиться в том, что она не завершается ошибкой, если в сеансе выполняется несколько раз.  

Но так как в конце хранимой процедуры нет команды `DROP TABLE`, после выполнения этой процедуры созданная таблица сохранится и ее можно будет читать вне хранимой процедуры.  

В пуле SQL, в отличие от других SQL Server баз данных, можно использовать временную таблицу вне процедуры, которая ее создала.  Временные таблицы пула SQL можно использовать в **любом месте** внутри сеанса. Эта функция может привести к более модульному и управляемому коду, как показано в следующем примере:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Ограничения временной таблицы
При реализации временных таблиц пул SQL накладывает ряд ограничений.  В настоящее время поддерживаются временные таблицы, которые можно просмотреть только в сеансе.  Глобальные временные таблицы не поддерживаются.  

Кроме того, представления не могут быть созданы во временных таблицах.  Временные таблицы можно создавать только с помощью хэш-распределения или циклического перебора.  Реплицированное временное распределение таблиц не поддерживается. 

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о разработке таблиц см. в статье [Проектирование таблиц с помощью ресурсов SQL Analytics](sql-data-warehouse-tables-overview.md) .

