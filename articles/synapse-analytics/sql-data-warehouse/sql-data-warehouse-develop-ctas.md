---
title: CREATE TABLE AS SELECT (CTAS)
description: Объяснение и примеры заявления CREATE TABLE AS SELECT (CTAS) в Synapse S'L для разработки решений.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: e5dc8835a6d5f235cf269edd4e9f069c904e1b7e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582148"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

В этой статье поясняется заявление CREATE TABLE AS SELECT (CTAS) T-S'L в Synapse S'L для разработки решений. Здесь также приведены примеры кодов.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Заявление [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) является одной из наиболее важных доступных функций T-S'L. CTAS — это параллельная операция, которая создает новую таблицу на основе вывода оператора SELECT. CTAS — это самый простой и быстрый способ создания и вставки данных в таблицу с одной командой.

## <a name="selectinto-vs-ctas"></a>Выберите... ИНТО vs. CTAS

CTAS является более настраиваемой версией [SELECT... Заявление INTO.](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Ниже приводится пример простого SELECT ... В:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Выберите... INTO не позволяет изменять ни метод распределения, ни тип индекса в рамках операции. Создается `[dbo].[FactInternetSales_new]` с помощью типа распределения по умолчанию ROUND_ROBIN и структуры таблицы по умолчанию CLUSTERED COLUMNSTORE INDEX.

С другой стороны, с помощью CTAS можно указать как распределение данных таблицы, так и тип структуры таблицы. Чтобы преобразовать предыдущий пример в CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Если вы только пытаетесь изменить индекс в операции CTAS и таблица исходного кода распределена, сохраните тот же столбик распределения и тип данных. Это позволяет избежать перекрестного распределения данных во время операции, что является более эффективным.

## <a name="use-ctas-to-copy-a-table"></a>Использование инструкции CTAS для копирования таблицы

Возможно, одним из наиболее распространенных видов использования CTAS является создание копии таблицы для того, чтобы изменить DDL. Допустим, вы изначально создали таблицу как `ROUND_ROBIN`, и теперь хотите изменить ее на таблицу, распределенную по столбце. CTAS — это способ изменения столбца распределения. Можно также использовать CTAS для изменения типов раздела, индексирования или столбцов.

Допустим, вы создали эту таблицу, используя `ROUND_ROBIN`тип распределения по умолчанию, `CREATE TABLE`не указывая столбец распределения в .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Теперь вы хотите создать новую копию `Clustered Columnstore Index`этой таблицы, с , так что вы можете воспользоваться производительностью таблиц кластерного columnstore. Вы также хотите распределить `ProductKey`эту таблицу на, потому что вы ожидаете соединения на `ProductKey`этом столбце и хотите, чтобы избежать движения данных во время соединения на . Наконец, вы также хотите добавить `OrderDateKey`раздел, так что вы можете быстро удалить старые данные, сбросив старые разделы. Вот заявление CTAS, которое копирует вашу старую таблицу в новую таблицу.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Наконец, вы можете переименовать свои таблицы, поменять в новой таблице, а затем отказаться от старого стола.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Используйте CTAS для работы с неподдерживаемыми функциями

Вы также можете использовать CTAS для работы над рядом неподдерживаемых функций, перечисленных ниже. Этот метод часто может оказаться полезным, потому что ваш код не только будет соответствовать, но он часто будет работать быстрее на Synapse S'L. Эта производительность является результатом его полностью параллельного дизайна. Сценарии включают в себя:

* Синтаксис соединения ANSI с операторами обновления
* Синтаксис соединения ANSI с операторами удаления
* Оператор MERGE

> [!TIP]
> Попробуйте думать "CTAS в первую очередь". Решение проблемы с помощью CTAS, как правило, хороший подход, даже если вы пишете больше данных в результате.

## <a name="ansi-join-replacement-for-update-statements"></a>Замена соединения ANSI для инструкций обновления

Вы можете обнаружить, что у вас есть сложное обновление. Обновление объединяет более двух таблиц вместе, используя SYNtax ANSI для выполнения UPDATE или DELETE.

Представьте, что вам необходимо обновить следующую таблицу:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Исходный запрос мог выглядеть примерно в этом примере:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Синапсе S'L не поддерживает присоединение ANSI в `FROM` пункте оператора, поэтому вы не можете использовать предыдущий `UPDATE` пример, не изменяя его.

Вы можете использовать комбинацию CTAS и неявного соединения, чтобы заменить предыдущий пример:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Замена синтаксиса соединения ANSI для операторов удаления

Иногда лучшим подходом для удаляния данных `DELETE` является использование CTAS, особенно для инструкций, которые используют синтаксис ANSI. Это происходит потому, что Synapse S'L не `FROM` поддерживает `DELETE` присоединение ANSI к пункту заявления. Вместо того, чтобы удалять данные, выберите данные, которые вы хотите сохранить.

Ниже приводится пример преобразованной `DELETE` оператора:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Замена операторов объединения

Вы можете заменить операторы слияния, по крайней мере частично, с помощью CTAS. Вы можете `INSERT` объединить `UPDATE` и в одно заявление. Любые удаленные записи `SELECT` должны быть ограничены из заявления, чтобы исключить результаты.

Следующий пример `UPSERT`для:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>явно указывайте тип данных и допустимость нулевого результата.

При мигрировании кода можно обнаружить, что вы столкнетесь с этим типом шаблона кодирования:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Вы можете подумать, что вы должны перенести этот код на CTAS, и вы были бы правы. Тем не менее, есть скрытая проблема здесь.

Следующий код не дает того же результата:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Обратите внимание, что столбец "result" переносит тип данных и допустимость значений NULL для выражения. Перенос типа данных вперед может привести к тонким отклонениям в значениях, если вы не будете осторожны.

Попробуйте этот пример:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Значение, сохраненное для результата, отличается. Поскольку сохраняемые значения в столбце результата используются в других выражениях, ошибка становится еще более значительной.

![Скриншот результатов CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Это важно для миграции данных. Несмотря на то, что второй запрос, возможно, более точен, есть проблема. Данные будут отличаться от исходной системы, что приводит к вопросам целостности миграции. Это один из тех редких случаев, когда "неправильный" ответ на самом деле является правильным!

Причина, по которой мы видим несоответствие между двумя результатами, связана с неявным литьем типа. В первом примере таблица определяет определение столбца. При вставке строки происходит неявное преобразование типа. Во втором примере нет неявного преобразования типа, поскольку выражение определяет тип данных столбца.

Обратите внимание также, что столбец во втором примере был определен как nULLable столбец, в то время как в первом примере он не имеет. Когда таблица была создана в первом примере, нулевость столбца была четко определена. Во втором примере он был оставлен на усмотрение выражения и по умолчанию приведет к определению NULL.

Чтобы решить эти проблемы, необходимо четко установить преобразование типа и нулевость в части выписки SELECT в выписке CTAS. Вы не можете установить эти свойства в 'CREATE TABLE'.
Следующий пример показывает, как исправить код:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Следует отметить следующее.

* Вы можете использовать CAST или CONVERT.
* Используйте ISNULL, а не COALESCE, чтобы заставить NULLability. Смотрите следующую заметку.
* ISNULL является внешней функцией.
* Вторая часть ISNULL является постоянной, 0.

> [!NOTE]
> Для того, чтобы недействительность была правильно установлена, очень важно использовать ISNULL, а не COALESCE. COALESCE не является детерминированной функцией, и поэтому результат выражения всегда будет nULLable. ISNULL работает иначе. Это детерминированно. Поэтому, когда вторая часть функции ISNULL является постоянной или буквальной, полученное значение не будет НЕДЕЙСТВИТЕЛЬНО.

Обеспечение целостности вычислений также важно для переключения разделов таблицы. Представьте, что эта таблица определяется как таблица фактов:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Тем не менее, поле количества является расчетным выражением. Это не входит в исходные данные.

Для создания разделенного набора данных может потребоваться использовать следующий код:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Запрос будет работать отлично. Проблема возникает, когда вы пытаетесь сделать переключатель перегородки. Определения таблицы не совпадают. Чтобы совмещеть определения таблицы, измените CTAS, чтобы добавить `ISNULL` функцию, чтобы сохранить атрибут недействительности столбца.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Вы можете видеть, что согласованность типа и поддержание свойств недействительности на CTAS является передовой инженерной практикой. Это помогает поддерживать целостность в расчетах, а также гарантирует, что переключение разделов возможно.

CTAS является одним из наиболее важных утверждений в Synapse S'L. Научитесь его применять. Смотрите [документацию CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Следующие шаги

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

