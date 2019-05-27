---
title: Инструкция CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure | Документация Майкрософт
description: Описание и примеры использования инструкции CREATE TABLE AS SELECT (CTAS) в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851447"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) в хранилище данных Azure SQL

В этой статье объясняется инструкции CREATE TABLE AS SELECT (CTAS) T-SQL в хранилище данных SQL Azure для разработки решений. В статье также содержатся примеры кода.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) инструкция (CTAS) является одним из наиболее важных доступных функций T-SQL. CTAS — это распараллеленная операция, которая создает новую таблицу на основе выходных данных инструкции SELECT. CTAS — это самый простой и быстрый способ создания и вставки данных в таблицу с помощью одной команды.

## <a name="selectinto-vs-ctas"></a>ВЫБЕРИТЕ... В vs. CTAS

CTAS — это расширенными возможностями настройки версия [ВЫБЕРИТЕ... В](/sql/t-sql/queries/select-into-clause-transact-sql) инструкции.

Ниже приведен пример простой запрос SELECT... В:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

ВЫБЕРИТЕ... В не позволяет изменить метод распределения или тип индекса в ходе операции. Создании `[dbo].[FactInternetSales_new]` с помощью по умолчанию тип распределения ROUND_ROBIN и КЛАСТЕРИЗОВАННЫМ ИНДЕКСОМ COLUMNSTORE структура таблицы по умолчанию.

С помощью CTAS с другой стороны, можно указать распределение данных таблицы, а также тип структуры таблицы. Чтобы преобразовать предыдущий пример, чтобы CTAS:

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
> Если только вы пытаетесь изменить индекс в операцию CTAS, а исходная таблица является хэш-распределение, ведение одного столбца и тип распределения. Это позволяет избежать перемещения данных между распространения во время операции, которая является более эффективным.

## <a name="use-ctas-to-copy-a-table"></a>Использование инструкции CTAS для копирования таблицы

Возможно одно из самых распространенных способов использования инструкции CTAS создает копию таблицы для изменения DDL. Давайте предположим, что вы изначально создали таблицу как `ROUND_ROBIN`и теперь хотите изменить его в таблицу с распределением по столбцу. CTAS является, как изменить столбец распределения. Можно также использовать CTAS для изменения секционирования, индексирования и типов столбцов.

Давайте предположим, что вы создали эту таблицу, используя тип распространения по умолчанию `ROUND_ROBIN`, не указав столбец распределения в `CREATE TABLE`.

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

Теперь вы хотите создать новую копию этой таблицы с `Clustered Columnstore Index`, позволяет вам воспользоваться преимуществами производительности кластеризованных таблиц Columnstore. Вы также хотите распределить эту таблицу на `ProductKey`, так как вы ожидаем соединения по этому столбцу и хотите избежать перемещения данных во время соединения на `ProductKey`. Наконец, вы также хотите добавить секционирование по `OrderDateKey`, поэтому можно быстро удалять старые данные путем удаления старых секций. Вот инструкцию CTAS, которая скопирует старую таблицу в новую таблицу.

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

Наконец вы можете переименовать таблицам, чтобы переключить в новой таблице, а затем удалите старую таблицу.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Использование инструкции CTAS для обхода неподдерживаемые функции

Можно также использование инструкции CTAS для замены некоторых неподдерживаемых функций, перечисленных ниже. Этот метод часто может оказаться полезным, так как не только ваш код будет совместимым, но его часто будет выполняться быстрее в хранилище данных SQL. Эти показатели производительности связано с распараллеленной структурой. Сценарии:

* Синтаксис соединения ANSI с операторами обновления
* Синтаксис соединения ANSI с операторами удаления
* Оператор MERGE

> [!TIP]
> Попробуйте подумать «CTAS первая.» Решение проблемы с помощью CTAS обычно является хорошим решением, даже если вы создаете больше данных в результате.

## <a name="ansi-join-replacement-for-update-statements"></a>Замена синтаксиса соединения ANSI для операторов обновления

Может оказаться, что у вас есть сложная операция обновления. Обновление соединяет более двух таблиц друг с другом с помощью синтаксиса соединения ANSI для выполнения операции UPDATE или DELETE.

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

Для замены в предыдущем примере, можно использовать сочетание CTAS и неявного соединения:

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

Иногда для удаления данных лучше всего использовать CTAS, особенно для `DELETE` синтаксисы объединения инструкции, использующие ANSI. Это обусловлено тем, хранилище данных SQL не поддерживает соединения ANSI в `FROM` предложении `DELETE` инструкции. Вместо удаления данных, выберите данные, которые вы хотите сохранить.

Ниже приведен пример преобразованного `DELETE` инструкции:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

Можно заменить инструкций merge, по крайней мере в части с помощью CTAS. Вы можете объединить `INSERT` и `UPDATE` в одну инструкцию. Любые удаленные записи должны быть лишены возможности `SELECT` инструкцию, чтобы исключить из результатов.

Следующий пример приведен для `UPSERT`:

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

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>явно указывайте тип данных и допустимость нулевого результата.

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

Вы можете подумать, этот код следует перенести в CTAS, и будете правы. Однако есть скрытая проблема.

Следующий код не дает тот же результат:

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

![Снимок экрана инструкции CTAS результатов](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Это важно для перемещения данных. Несмотря на то, что второй запрос является более точным, возникла проблема. Данные будут отличаться по сравнению с исходной системой, и это приведет к вопросам целостности при миграции. Это один из тех редких случаев, когда "неправильный" ответ на деле оказывается верным!

Причина, вы заметите несоответствие между двумя результатами заключается в том, из-за неявном преобразовании типов. В первом примере в таблице приводится определение столбца. При вставке строки происходит неявное преобразование типов. В втором примере отсутствует неявное преобразование типов как выражение определяет тип данных столбца.

Обратите внимание, что столбец во втором примере определен как столбец допускает значения NULL, тогда как в первом примере не было. При создании таблицы в первом примере была явно указана допустимость значений NULL столбца. Во втором примере была оставлена в выражение она по умолчанию приведет к определению значение NULL.

Чтобы устранить эти проблемы, необходимо явно задать преобразование типов и допустимость значений NULL в части SELECT инструкции CTAS. Эти свойства нельзя задать в инструкции CREATE TABLE.
В следующем примере показано, как исправить код:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Обратите внимание на следующее условия:

* Можно использовать ПРИВЕДЕНИЕ или преобразование.
* Используйте ISNULL, COALESCE, чтобы принудительно допустимость значений NULL. См. примечание ниже.
* Функция ISNULL является внешней функцией.
* Вторая часть функции ISNULL является константой, 0.

> [!NOTE]
> Для правильной настройки допустимости значений NULL крайне важно использовать ISNULL, а не COALESCE. COALESCE не является детерминированной функцией, и поэтому результат выражения всегда будет иметь NULLable. ISNULL работает иначе. Она является детерминированной. Таким образом, если вторая часть функции ISNULL является константой или литералом, результирующим значением будет NOT NULL.

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

Тем не менее поле Сумма — это вычисляемое выражение. Он не является частью исходных данных.

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

Этот запрос будет выполнен нормально. Проблема возникает, когда вы пытаетесь выполнить переключение секций. Определения таблиц не совпадают. Для определения таблиц совпадают, измените инструкции CTAS, чтобы добавить `ISNULL` функции, чтобы сохранить атрибут допустимости значений NULL столбца.

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

Вы увидите, что обеспечения согласованности типов и поддержания допустимости инструкцию ctas является технической практикой, рекомендации. Он помогает поддерживать целостность вычислений и также гарантирует, что переключение секций невозможно.

CTAS — одна из самых важных операторов в хранилище данных SQL. Научитесь его применять. См. в разделе [CTAS документации](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке см. в разделе [Общие сведения о разработке](sql-data-warehouse-overview-develop.md).

