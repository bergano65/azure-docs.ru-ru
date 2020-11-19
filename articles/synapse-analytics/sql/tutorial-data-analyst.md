---
title: Руководство по Анализ Открытых наборов данных Azure в Azure Synapse Studio (предварительная версия) с помощью бессерверного пула SQL (предварительная версия)
description: В этом руководстве показано, как без усилий выполнять исследовательский анализ данных, объединяя разные Открытые наборы данных Azure с помощью бессерверного пула SQL (предварительная версия), и визуализировать результаты в Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 84fc49df2838a66969b449dee5b416c2a0f86f86
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685925"
---
# <a name="tutorial-use-serverless-sql-pool-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Руководство по Анализ Открытых наборов данных Azure и визуализация результатов в Azure Synapse Studio с помощью бессерверного пула SQL

В этом руководстве показано, как выполнять исследовательский анализ данных, объединяя различные Открытые наборы данных Azure с помощью бессерверного пула SQL, а затем визуализировать результаты в Azure Synapse Studio.

В частности, вы проанализируете [набор данных о такси Нью-Йорка](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), который включает следующие данные:

- Даты и время посадки и высадки пассажиров.
- Пункты посадки и высадки. 
- Расстояния поездок.
- Детализированные пассажирские тарифы.
- Виды тарифов.
- Виды оплаты. 
- Отчеты водителей о количестве пассажиров.

## <a name="automatic-schema-inference"></a>Автоматический вывод схемы

Данные хранятся в файлах формата Parquet, поэтому вам доступен автоматический вывод схемы. Таким образом, вы можете легко запрашивать информацию без перечисления типов данных для всех столбцов в файлах. Кроме того, можно отфильтровать определенное подмножество файлов, используя механизм виртуальных столбцов и функцию filepath.

Сначала ознакомимся с данными о такси Нью-Йорка, выполнив следующий запрос:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Результаты запроса данных о такси Нью-Йорка:

![Результаты запроса данных о такси Нью-Йорка](./media/tutorial-data-analyst/1.png)

Аналогичным образом можно запросить набор данных о государственных праздниках:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Результаты запроса для набора данных о государственных праздниках:

![Результаты запроса для набора данных о государственных праздниках](./media/tutorial-data-analyst/2.png)

Аналогичным образом можно запросить набор данных о погоде:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Результаты запроса для набора данных о погоде:

![Результаты запроса для набора данных о погоде](./media/tutorial-data-analyst/3.png)

Вы можете получить дополнительные сведения о значении отдельных столбцов в описаниях наборов данных [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) (такси Нью-Йорка), [Public Holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) (государственные праздники) и [Weather Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) (данные о погоде).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Анализ временных рядов, сезонности и выбросов

Ежегодное количество поездок на такси можно легко подсчитать с помощью следующего запроса:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Результаты запроса данных о ежегодном количестве поездок на такси:

![Результаты запроса данных о ежегодном количестве поездок на такси](./media/tutorial-data-analyst/4.png)

Данные можно визуализировать в Synapse Studio. Для этого переключитесь из представления **Таблица** в представление **Диаграмма**. В нем доступны разные типы диаграмм: **С областями**, **Линейчатая**, **Гистограмма**, **Линейная**, **Круговая**, **Точечная**. В этом случае построим **гистограмму**, у которой в столбце **Категория** задано значение **current_year** (текущий_год):

![Гистограмма с данными о ежегодном количестве поездок](./media/tutorial-data-analyst/5.png)

По этой визуализации можно четко проследить тенденцию к снижению ежегодного числа поездок. Предположительно, причина заключается в недавно возросшей популярности сервисов совместных поездок.

> [!NOTE]
> На момент написания этого руководства данные за 2019 год были неполными. Поэтому для этого года суммарное количество поездок на такси значительно меньше, чем для других.

Теперь сосредоточим наш анализ на одном определенном годе, например 2016. Это запрос, возвращающий количество поездок за каждый день этого года:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Результаты этого запроса:

![Результаты запроса данных о ежедневном количестве поездок за 2016 год](./media/tutorial-data-analyst/6.png)

Опять же, можно легко визуализировать данные, построив **гистограмму**. В столбце **Категория** зададим значение **current_day** (текущий_день), а в столбце **Условные обозначения (ряды)**  — **rides_per_day** (поездок_в_день).

![Гистограмма с данными о ежедневном количестве поездок за 2016 год](./media/tutorial-data-analyst/7.png)

По графику можно наблюдать недельную периодичность колебаний количества поездок с пиком в субботу. В летние месяцы в связи с периодом отпусков число поездок уменьшается. Кроме того, обратите внимание на ряд значительных спадов без периодичности, причина которых непонятна.

Теперь давайте посмотрим, коррелируют ли эти спады с государственными праздниками. Объединим набор данных о поездках на такси Нью-Йорка с набором данных о государственных праздниках:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Визуализация результата объединения наборов данных о поездках на такси Нью-Йорка и о государственных праздниках](./media/tutorial-data-analyst/8.png)

Теперь нам нужно выделить число поездок на такси в дни государственных праздников. Выберем для столбца **Категория** значение **none** (нет), а для столбцов **Условные обозначения** (ряды) — **rides_per_day** (поездок_в_день) и **holiday** (праздник).

![График количества поездок на такси в дни государственных праздников](./media/tutorial-data-analyst/9.png)

По графику видно, что в дни государственных праздников количество поездок на такси уменьшается. Но по-прежнему остается необъясненным огромный спад 23 января. Давайте проверим погоду в Нью-Йорке в этот день, запросив набор данных о погоде:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Визуализация результатов запроса для набора данных о погоде](./media/tutorial-data-analyst/10.png)

Результаты запроса указывают на такие причины уменьшения количества поездок:

- В этот день в Нью-Йорке была метель и выпало много снега (около 30 см).
- Было холодно (ниже нуля по Цельсию).
- Было ветрено (скорость ветра около 10 м/с).

Из этого руководства для специалистов по анализу данных вы узнали, как быстро выполнять исследовательский анализ данных, легко объединять наборы данных с помощью бессерверного пула SQL и визуализировать результаты в Azure Synapse Studio.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, [как подключить бессерверный пул SQL к Power BI Desktop и создавать отчеты](tutorial-connect-power-bi-desktop.md).
 
