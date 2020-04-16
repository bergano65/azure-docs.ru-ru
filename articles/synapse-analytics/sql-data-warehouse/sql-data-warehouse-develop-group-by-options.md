---
title: Использование группы по опциям
description: Советы по реализации группы по опциям в пуле Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d8d4c6d47e33ca365415542c2da9779b4d7d1dd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416201"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Группа по опциям в бассейне Synapse S'L

В этой статье вы найдете советы по реализации группы по вариантам в пуле S'L.

## <a name="what-does-group-by-do"></a>Предназначение предложения GROUP BY

Предложение T-SQL [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) используется для объединения данных в сводный набор строк. Group BY имеет несколько вариантов, которые бассейн S'L не поддерживает. Эти варианты имеют обходные пути, которые являются следующими:

* GROUP BY с ROLLUP;
* GROUPING SETS
* GROUP BY с CUBE.

## <a name="rollup-and-grouping-sets-options"></a>Параметры Rollup и Grouping Sets

Самый простой вариант здесь заключается в использовании UNION ALL для выполнения свертывания, а не полагаться на явный синтаксис. Результат точно такой же.

Пример использования инструкции GROUP BY с оператором ROLLUP.

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Используя ROLLUP, приведенный выше пример запрашивает следующие объединения.

* Страна и регион
* Country
* Grand Total

Чтобы получить те же результаты без ROLLUP, можно использовать оператор UNION ALL и явным образом указать требуемые объединения.

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Для замены GROUPING SETS применяется тот же принцип. Достаточно создать разделы с UNION ALL для требуемых уровней объединения.

## <a name="cube-options"></a>Параметры Cube

С помощью подхода UNION ALL можно создать GROUP BY WITH CUBE. Проблема в том, что в этом случае код может быстро разрастись и стать слишком громоздким. Чтобы смягчить эту проблему, можно использовать этот более продвинутый подход.

Используя предыдущий пример, первым шагом является определение «кубика», который определяет все уровни агрегации, которые мы хотим создать.

Обратите внимание на CROSS JOIN из двух производных таблиц, так как это генерирует все уровни для нас. Остальная часть кода предназначена для форматирования:

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

На следующем изображении показаны результаты CTAS:

![GROUP BY с CUBE](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Вторым шагом является определение целевой таблицы для хранения промежуточных результатов:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Третий шаг — запустить цикл для куба столбцов, участвующих в группировании данных. Запрос будет работать один раз для каждой строки в #Cube временной таблице. Результаты хранятся в таблице #Results темпа:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Наконец, вы можете вернуть результаты, прочитав с #Results временную таблицу:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Разбивая код на разделы и создавая конструкцию циклов, код становится более управляемым и обслуживаемым.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).
