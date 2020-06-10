---
title: Руководство по подготовке данных к выполнению кластеризации на языке R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: В первой части этого руководства из трех частей вы узнаете, как подготовить данные из базы данных SQL Azure для выполнения кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c06e1b13f87972cbcd50e888edf55158b77881d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84024101"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство по Подготовка данных для выполнения кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

В первой части этого руководства, состоящего из трех частей, объясняется, как импортировать и подготовить данные из базы данных SQL Azure с помощью R. Далее в этой серии эти данные будут использоваться для обучения и развертывания модели кластеризации на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

*Кластеризацию* можно описать как организацию данных по группам, где члены группы каким-либо образом похожи друг на друга.
Вы будете использовать метод **k-средних** для кластеризации клиентов в наборе данных о покупках и возвратах продуктов. Благодаря кластеризации клиентов вы можете более эффективно осуществлять маркетинговую деятельность, ориентируясь на конкретные группы.
Кластеризация методом k-средних — это алгоритм *неконтролируемого обучения*, который ищет закономерности в данных на основе сходства.

В первой и второй части из этой серии описывается, как создать некоторые скрипты R в RStudio для подготовки данных и обучения модели машинного обучения. Из третьей части вы узнаете, как выполнить эти скрипты R в базе данных SQL с помощью хранимых процедур.

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
>
> * Импорт образца базы данных в Базу данных SQL Azure
> * Разделение клиентов по разным измерениям с использованием языка R.
> * Загрузка данных из Базы данных SQL Azure в кадр данных на языке R.

Из [второй части](clustering-model-build-tutorial.md) вы узнаете, как создать и обучить модель кластеризации по методу K-средних на языке R.

В [третьей части](clustering-model-deploy-tutorial.md) рассказывается, как создать в Базе данных SQL Azure хранимую процедуру, с помощью которой можно выполнять кластеризацию с помощью R на основе новых данных.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

* [База данных SQL Azure с включенными Службами машинного обучения](machine-learning-services-overview.md) (с использованием R).

* Пакет RevoScaleR. Сведения о вариантах локальной установки этого пакета см. в разделе [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms).

* IDE R. В этом учебнике используется [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Инструмент SQL-запроса. В этом учебнике предполагается, что вы используете [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) или [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Импорт образца базы данных

Пример набора данных, используемый в этом учебнике, сохранен в **BACPAC-файле** резервной копии базы данных, доступный для скачивания и использования. Этот набор данных является производным от набора данных [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp), предоставляемого [Советом по оценке производительности обработки транзакций (TPC)](http://www.tpc.org/default.asp).

1. Скачайте файл [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Следуйте указаниям в статье [Quickstart: Import a BACPAC file to a database in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-import) (Краткое руководство. Импорт BACPAC-файла в базу данных в Базе данных SQL Azure), используя следующие сведения:

   * Выполните импорт данных из скачанного файла **tpcxbb_1gb.bacpac**.
   * В общедоступной предварительной версии выберите конфигурацию **виртуального ядра 5-го поколения** для новой базы данных.
   * Присвойте новой базе данных имя "tpcxbb_1gb".

## <a name="separate-customers"></a>Разделение клиентов

Создайте файл RScript в RStudio и выполните следующий сценарий.
В SQL-запросе разделение клиентов выполняется по таким измерениям:

* **orderRatio** = коэффициент возвратов заказов (отношение числа частично или полностью возвращенных заказов к общему числу заказов)
* **itemsRatio** = коэффициент возврата единицы товара (отношение возвращенных единиц товара к общему числу проданных единиц товара)
* **monetaryRatio** = коэффициент возврата в денежном выражении (отношение общего объема возвратов к общему объему покупок в денежном выражении)
* **frequency** = частота возвратов

В функции **paste** замените **Server**, **UID** и **PWD** собственными данными для подключения.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Загрузка данных в кадр данных

Теперь воспользуйтесь следующим скриптом, чтобы возвратить результаты из запроса в кадр данных на языке R с помощью функции **rxSqlServerData**.
В ходе процесса вы также определите тип для выбранных столбцов (с помощью аргумента colClasses), чтобы обеспечить корректную передачу типов в R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Результат должен иметь следующий вид.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Очистка ресурсов

***Если вы не собираетесь продолжать работу с этим руководством***, удалите базу данных tpcxbb_1gb со своего сервера.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени…** введите **tpcxbb_1gb** и выберите свою подписку.
1. Выберите базу данных **tpcxbb_1gb**.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В первой части этого учебника вы выполнили следующие действия:

* Импорт образца базы данных в базу данных SQL Azure.
* Разделение клиентов по разным измерениям с использованием языка R.
* Загрузка данных из Базы данных SQL Azure в кадр данных на языке R.

Чтобы создать модель машинного обучения, использующую эти данные клиента, воспользуйтесь инструкциями из второй части этой серии руководств:

> [!div class="nextstepaction"]
> [Руководство по созданию прогнозной модели на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия)](clustering-model-build-tutorial.md)
