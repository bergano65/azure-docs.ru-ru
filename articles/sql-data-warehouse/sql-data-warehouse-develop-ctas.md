---
title: Инструкция CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure | Документация Майкрософт
description: Описание и примеры с использованием инструкции CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: ea95a13277927b485bb9da3b75b84cce4337bf88
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280440"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) в хранилище данных Azure SQL

Описание и примеры кода с помощью инструкции CREATE TABLE AS SELECT (CTAS) T-SQL в хранилище данных SQL Azure для разработки решений.

## <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

Инструкция [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) или CTAS — одна из самых важных функций T-SQL. CTAS — это распараллеленная операция, которая создает новую таблицу на основе выходных данных инструкции SELECT. CTAS — это самый простой и быстрый способ создания и вставки данных в таблицу с помощью одной команды.

## <a name="selectinto-vs-ctas"></a>Сравнение SELECT..INTO и CTAS;

CTAS представляет собой сильно расширенную версию [ВЫБЕРИТЕ... В](/sql/t-sql/queries/select-into-clause-transact-sql) инструкции.

Ниже показан пример простой инструкции SELECT...INTO.

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

ВЫБЕРИТЕ... В не позволяют изменить метод распределения или тип индекса в ходе операции. `[dbo].[FactInternetSales_new]` создается с использованием типа распределения по умолчанию ROUND_ROBIN и структура таблицы по умолчанию как КЛАСТЕРИЗОВАННЫЙ индекс COLUMNSTORE.

С помощью CTAS, имеется возможность указать оба типа распределения данных таблицы, а также структуру таблицы. Чтобы преобразовать предыдущий пример, чтобы CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Если вы просто пытаетесь изменить индекс в операции `CTAS` и исходная таблица использует хэш-распределение, то для выполнения операции `CTAS` будет лучше, если вы оставите тот же столбец распределения и тип данных. Это позволит избежать перемещения данных для перекрестного распределения во время операции, что повысит ее эффективность.

## <a name="using-ctas-to-copy-a-table"></a>Копирование таблицы с помощью CTAS

Возможно одно из наиболее распространенных использует `CTAS` Создание копии таблицы для изменения DDL. Если, например, вы изначально создали таблицу как `ROUND_ROBIN` и теперь хотите изменить его в таблицу с распределением по столбцу, `CTAS` является, как изменить столбец распределения. `CTAS` может также использоваться для изменения секционирования, индексирования и типов столбцов.

Предположим, вы создали эту таблицу, с использованием типа распределения по умолчанию `ROUND_ROBIN` , не указав столбец распределения в `CREATE TABLE`.

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
    CustomerPONumber nvarchar(25)
);
```

Теперь вам нужно создать новую копию этой таблицы с кластеризованным индексом Columnstore, чтобы воспользоваться преимуществами производительности кластеризованных таблиц Columnstore. Также требуется распространить эту таблицу по ProductKey, поскольку ожидаются соединения по этому столбцу и требуется избежать перемещения данных во время операций соединения по ProductKey. Наконец вы захотите добавить секционирование по столбцу OrderDateKey, таким образом, чтобы быстро удалять старые данные путем удаления старых секций. Вот инструкция CTAS, которая скопирует старую таблицу в новую таблицу.

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

Наконец, можно переименовать таблицу, чтобы переключиться на новую таблицу, а затем удалить старую таблицу.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>Обход неподдерживаемых функций с помощью CTAS

CTAS можно также использовать для замены некоторых неподдерживаемых функций, указанных ниже. Этот метод часто может оказаться в ситуации удачным, как не только ваш код будет совместимым, но он будет часто выполняются быстрее в хранилище данных SQL. Эти показатели производительности связано с распараллеленной структурой. Сценарии, которые можно обойти с помощью CTAS:

* Синтаксис соединения ANSI с операторами обновления
* Синтаксис соединения ANSI с операторами удаления
* Оператор MERGE

> [!NOTE]
> Старайтесь всегда начинать с функции CTAS. Решение проблемы с помощью `CTAS` — это обычно является хорошим решением, даже если вы создаете больше данных в результате.

## <a name="ansi-join-replacement-for-update-statements"></a>Замена синтаксиса соединения ANSI для операторов обновления

Вы можете обнаружить, что у вас есть сложная операция обновления, объединяющий более чем двумя таблицами, с помощью синтаксиса соединения ANSI для выполнения операции UPDATE или DELETE.

Допустим, вам нужно обновить следующую таблицу:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Исходный запрос может выглядеть примерно как в следующем примере:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Хранилище данных SQL не поддерживает соединения ANSI в `FROM` предложении `UPDATE` инструкции, поэтому ее невозможно использовать предыдущий пример, не изменяя его.

Можно использовать сочетание `CTAS` и неявного соединения для замены в предыдущем примере:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Замена синтаксиса соединения ANSI для операторов удаления

Иногда для удаления данных лучше использовать `CTAS`, особенно для `DELETE` синтаксисы объединения инструкции, использующие ANSI. Это обусловлено тем, хранилище данных SQL не поддерживает соединения ANSI в `FROM` предложении `DELETE` инструкции. Вместо удаления данных, выберите данные, которые вы хотите сохранить.

Пример преобразованного оператора DELETE:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Замена операторов объединения

CTAS может (по крайней мере частично) заменить инструкции объединения. Можно объединить в одной инструкции INSERT и UPDATE. Любые удаленные записи должны быть лишены возможности `SELECT` инструкцию, чтобы исключить из результатов.

Следующий пример приведен для операции UPSERT:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Рекомендация по использованию функции CTAS: явно указывайте тип данных и допустимость нулевого результата.

При переносе кода, возможно, стоек, такого рода шаблон кодирования:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Вы можете инстинктивно решить, что этот код нужно перенести в CTAS, и будете правы. Однако есть скрытая проблема.

Представленный ниже код НЕ дает такой же результат:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Обратите внимание, что в столбец "результат" переходит тип данных и допустимость нулевых значений выражения. Хранение данных вперед типа может привести к различиям в значениях следует соблюдать осторожность.

Рассмотрим следующий пример.

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Значение, сохраненное в качестве результата, будет иным. Так как сохраненное значение в столбце результатов используется в других выражениях, ошибка становится еще более значительной.

![Результаты CTAS](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Это важно для перемещения данных. Несмотря на то, что второй запрос является более точным возникла проблема. Полученные данные будут разными в разных системах, что ставит под сомнение достоверность перенесенных данных. Это один из тех редких случаев, когда "неправильный" ответ на деле оказывается верным!

Причина, вы заметите несоответствие между двумя результатами заключается в том, из-за неявном преобразовании типов. В первом примере в таблице приводится определение столбца. При вставке строки происходит неявное преобразование типов. В втором примере отсутствует неявное преобразование типов как выражение определяет тип данных столбца. Обратите внимание, что во втором примере столбец определен как допускающий нулевое значение, а в первом нет. При создании таблицы в первом примере была явно указана допустимость значений NULL столбца. Во втором примере она была оставлена в выражение и по умолчанию, приведет к определению NULL.

Чтобы устранить эти проблемы, необходимо явно задать преобразование типов и допустимость значений NULL в части SELECT инструкции CTAS. Эти свойства невозможно установить в части создания таблицы.

В приведенном ниже примере показано, как исправить код:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Обратите внимание на следующее условия:

* Можно было использовать CAST или CONVERT.
* Чтобы обеспечить допустимость значений NULL, используется ISNULL, а не COALESCE.
* ISNULL является внешней функцией.
* Вторая часть функции ISNULL является константой, 0

> [!NOTE]
> Для правильного задания допустимости нулевых значений необходимо использовать ISNULL, а не COALESCE. COALESCE не является детерминированной функцией, поэтому выражение всегда будет допускать нулевые значения. ISNULL работает иначе. Он детерминирован, поэтому если вторая часть функции ISNULL является константой или литералом, итоговым значением будет NOT NULL.

Обеспечить целостность вычислений также важна для переключения секций таблицы. Представьте, что эта таблица, определенные как таблица фактов:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Тем не менее поле «Сумма» является вычисляемым выражением; он не является частью исходных данных.

Для создания набора секционированных данных, вы можете использовать следующий код:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Такой запрос выполняется нормально. Проблема возникает, когда вы пытаетесь выполнить переключение секций. Определения таблиц не совпадают. Чтобы определения таблиц совпадали, необходимо изменить, чтобы добавить CTAS `ISNULL` функции, чтобы сохранить атрибут допустимости значений NULL столбца.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Вы увидите, что обеспечения согласованности типов и поддержания допустимости инструкцию ctas рекомендуется проектирования рекомендации. Это обеспечивает правильность вычислений и возможность переключения между разделами.

Ссылаться на [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) документации. CTAS — одна из самых важных операторов в хранилище данных SQL Azure. Научитесь его применять.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

