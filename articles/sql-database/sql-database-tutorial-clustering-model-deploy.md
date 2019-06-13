---
title: Руководство. Развертывание модели кластеризации на языке R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: В последней части этого цикла из трех руководств вы развернете модель кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420222"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство. Развертывание модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)

В последней части этого цикла из трех руководств вы развернете модель кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

Вы создадите хранимую процедуру с внедренным сценарием R, который выполняет кластеризацию. Так как ваша модель выполняется в Базе данных SQL Azure, ее легко можно обучить с помощью данных, хранящихся в базе данных.

Из этой статьи вы узнаете о следующем:

> [!div class="checklist"]
> * Создание хранимой процедуры, которая создает модель
> * Выполнение кластеризации в Базе данных SQL
> * Использование сведений о кластеризации

Из [первой части](sql-database-tutorial-clustering-model-prepare-data.md) вы узнали, как выполнять подготовку данных из Базы данных SQL Azure для кластеризации на языке R.

Из [второй части](sql-database-tutorial-clustering-model-build.md) вы узнали, как создать модель к-средних для выполнения кластеризации.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* В третьей части этой серии учебников предполагается, что вы ознакомились с [**первой**](sql-database-tutorial-clustering-model-prepare-data.md) и [**второй частью**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Создание хранимой процедуры, которая создает модель

Чтобы создать хранимую процедуру, выполните следующий сценарий T-SQL. Эта процедура воссоздает действия, разработанные в частях 1 и 2 этого цикла руководств:

* классификация клиентов по истории покупок и возвратов;
* создание четырех кластеров клиентов с помощью алгоритма k-средних.

Процедура сохраняет полученные сопоставления клиентов с кластерами в таблице базы данных **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Выполнение кластеризации в Базе данных SQL

Теперь, создав хранимую процедуру, выполните следующий сценарий для выполнения кластеризации.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Убедитесь, что он работает и что у нас действительно имеется список клиентов и их сопоставлений с кластерами.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Использование сведений о кластеризации

Так как вы сохранили процедуру кластеризации в базе данных, она может эффективно выполнять кластеризацию данных клиентов, хранящихся в той же базе данных. При изменении данных клиентов вы можете выполнить эту процедуру и использовать обновленную информацию о кластеризации.

Предположим, вам нужно отправлять рекламные сообщения клиентам в кластере 3 — группе с наиболее активными возвратами (описание всех четырех кластеров приведено во [второй части](sql-database-tutorial-clustering-model-build.md#analyze-the-results)). Приведенный ниже код выбирает адреса электронной почты клиентов в кластере 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Вы можете изменить значение **r.cluster**, чтобы возвращать адреса электронной почты клиентов в других кластерах.

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив работу с этим руководством, вы можете удалить базу данных tpcxbb_1gb с сервера Базы данных SQL Azure.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени…** введите **tpcxbb_1gb** и выберите свою подписку.
1. Выберите базу данных **tpcxbb_1gb**.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В рамках третьей части этой серии учебников вы выполнили такие задачи:

* Создание хранимой процедуры, которая создает модель
* Выполнение кластеризации в Базе данных SQL
* Использование сведений о кластеризации

Дополнительные сведения об использовании языка R в Службе машинного обучения Базы данных SQL Azure (предварительная версия) см. в следующих статьях:

* [Руководство. Подготовка данных для обучения прогнозной модели на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](sql-database-machine-learning-services-functions.md)
* [Work with R and SQL data in Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md) (Работа с данными R и SQL в Службах машинного обучения в Базе данных SQL Azure)
* [Добавление пакета R в Службу машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-machine-learning-services-add-r-packages.md)
