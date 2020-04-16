---
title: Используйте временные таблицы с Synapse S'L
description: Основные рекомендации по использованию временных таблиц в Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428762"
---
# <a name="temporary-tables-in-synapse-sql"></a>Временные таблицы в Synapse S'L

В этой статье содержатся основные рекомендации по использованию временных таблиц и освещаются принципы временных таблиц уровня сеанса в synapse S'L. 

Ресурсы по запросу (предварительный просмотр) могут использовать временные таблицы как в пуле S'L, так и на ресурсах по требованию. В конце этой статьи есть ограничения по запросу. 

## <a name="what-are-temporary-tables"></a>Что собой представляют временные таблицы

Временные таблицы полезны при обработке данных, особенно во время преобразования, когда промежуточные результаты являются временными. С Synapse S'L на уровне сеанса существуют временные таблицы.  Они видны только на сеансе, в котором они были созданы. Таким образом, они автоматически отключается при отключении сеанса. 

## <a name="temporary-tables-in-sql-pool"></a>Временные таблицы в бассейне S'L

В ресурсе пула S'L временные таблицы обеспечивают преимущества производительности, поскольку их результаты записываются в локальное, а не удаленное хранилище.

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

### <a name="dropping-temporary-tables"></a>Удаление временных таблиц
При создании сеанса не должно быть ни одной временной таблицы.  Однако, если вы вызываете ту же процедуру хранения, которая создает `CREATE TABLE` временное с тем же именем, чтобы убедиться, что ваши операторы являются успешными, используйте простую проверку до существования с: `DROP` 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Для согласованности кодирования является хорошей практикой использование этого шаблона как для таблиц, так и для временных таблиц.  Кроме того, рекомендуется использовать `DROP TABLE` временные таблицы, когда вы закончите с ними.  

При разработке сохраненной процедуры обычно можно увидеть команды капли в комплекте в конце процедуры, чтобы обеспечить очистку этих объектов.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Разделение кода на модули
Временные таблицы могут использоваться в любом месте сеанса пользователя. Эта возможность может быть использована, чтобы помочь вам модульной код приложения.  Чтобы продемонстрировать, следующая сохраненная процедура генерирует DDL для обновления всех статистических данных в базе данных по статистическому названию:

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

На данном этапе единственным действием, которое произошло, является создание сохраненной процедуры, которая генерирует #stats_ddl временную таблицу.  Сохраненная процедура падает #stats_ddl, если она уже существует. Это падение гарантирует, что он не выведет из строя, если выполнить более одного раза в течение сессии.  

Поскольку в конце `DROP TABLE` сохраненной процедуры нет, когда процедура хранения завершается, созданная таблица остается и может быть прочитана вне процедуры хранения.  

В отличие от других баз данных сервера S'L, Synapse S'L позволяет использовать временную таблицу вне процедуры, которая ее создала.  Временные таблицы, созданные с помощью пула S'L, могут использоваться в **любом месте** сеанса. В результате вы получите более модульный и управляемый код, как показано в приведенном ниже примере:

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

### <a name="temporary-table-limitations"></a>Ограничения временной таблицы

В пуле S'L есть несколько ограничений по реализации временных таблиц:

- Поддерживаются только временные таблицы, охвативающие сеанс.  Глобальные временные таблицы не поддерживаются.
- Представления не могут быть созданы на временных таблицах.
- Временные таблицы могут быть созданы только с хэш или круглым распределением робина.  Репликация временного распределения таблиц не поддерживается. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Временные таблицы в S'L по запросу (предварительный просмотр)

Временные таблицы в S'L по требованию поддерживаются, но их использование ограничено. Они не могут быть использованы в запросах, которые нацелены на файлы. 

Например, нельзя присоединиться к временной таблице с данными из файлов в хранилище. Количество временных таблиц ограничено 100, а их общий размер ограничен 100 МБ.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о разработке таблиц, см. [таблицы проектирования с помощью статьи ресурсов Synapse S'L.](develop-tables-overview.md)

