---
title: Руководство для специалистов по анализу данных. Анализ Открытых наборов данных Azure в Azure Synapse Studio (предварительная версия) с помощью службы SQL по запросу (предварительная версия)
description: Из этого руководства вы узнаете, как легко выполнять исследовательский анализ данных, объединяя различные Открытые наборы данных Azure с помощью службы SQL по запросу (предварительная версия), и визуализировать результаты в Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426283"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Анализ Открытых наборов данных Azure и визуализация результатов в Azure Synapse Studio (предварительная версия) с помощью службы SQL по запросу (предварительная версия)

Из этого руководства вы узнаете, как выполнять исследовательский анализ данных, объединяя различные Открытые наборы данных Azure с помощью службы SQL по запросу, а затем визуализировать результаты в Azure Synapse Studio.

В частности, вы проанализируете [набор данных такси Нью-Йорка](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), включающий такие данные, как дата и время посадки и высадки пассажиров, пункты посадки и высадки, расстояния поездок, детализированные пассажирские тарифы, виды тарифа, виды оплаты и отчеты водителей о количестве пассажиров.

Анализ будет направлен на поиск тенденций в изменении числа поездок на такси в зависимости от времени. Вы проанализируете два других Открытых набора данных Azure ([государственные праздники](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) и [данные о погоде](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)), чтобы определить причину выбросов в количестве поездок на такси.

## <a name="create-credentials"></a>Создание учетных данных

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Автоматический вывод схемы

Так как данные хранятся в формате файла Parquet, доступен автоматический вывод схемы. Поэтому можно легко запрашивать данные без необходимости перечислять типы данных всех столбцов в файлах. Кроме того, можно отфильтровать определенное подмножество файлов, используя механизм виртуальных столбцов и функцию filepath.

Сначала ознакомимся с данными такси Нью-Йорка, выполнив следующий запрос:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Ниже показан фрагмент результата для данных такси Нью-Йорка.

![фрагмент результата](./media/tutorial-data-analyst/1.png)

Аналогичным образом можно запросить данные о государственных праздниках, используя следующий запрос к набору данных:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Ниже показан фрагмент результата для набора данных о государственных праздниках.

![фрагмент результата 2](./media/tutorial-data-analyst/2.png)

Аналогичным образом можно запросить данные о погоде, используя следующий запрос к набору данных:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Ниже показан фрагмент результата для набора данных о погоде.

![фрагмент результата 3](./media/tutorial-data-analyst/3.png)

Вы можете получить дополнительные сведения о значении отдельных столбцов в описаниях наборов данных [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) (такси Нью-Йорка), [Public Holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) (государственные праздники) и [Weather Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) (данные о погоде).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Анализ временных рядов, сезонности и выбросов

Вы можете легко подсчитать ежегодное количество поездок на такси с помощью следующего запроса:

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

Ниже показан фрагмент результата для ежегодного количества поездок на такси.

![фрагмент результата 4](./media/tutorial-data-analyst/4.png)

Данные можно визуализировать в Synapse Studio. Для этого переключитесь из представления "Таблица" в представление "Диаграмма". Вы можете выбрать один из разных типов диаграмм (c областями, линейчатая, гистограмма, линейная, круговая и точечная). В этом случае построим гистограмму, у которой в столбце "Категория" задано значение "current_year" (текущий_год):

![визуализация результата 5](./media/tutorial-data-analyst/5.png)

По этой визуализации можно четко проследить тенденцию снижения числа поездок с каждым годом, предположительно из-за недавнего роста популярности сервисов совместных поездок.

> [!NOTE]
> На момент написания этого руководства данные за 2019 год были неполными. Поэтому для этого года суммарное количество поездок на такси значительно меньше, чем для других.

Теперь сосредоточим наш анализ на одном отдельном годе, например 2016 годе. По следующему запросу возвращается количество поездок за каждый день этого года:

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

Ниже показан фрагмент результата для этого запроса.

![фрагмент результата 6](./media/tutorial-data-analyst/6.png)

Опять же, можно легко визуализировать данные, построив гистограмму, у которой в столбце категории задано значение "current_day" (текущий_день), а в столбце условных обозначений (ряды) — "rides_per_day" (поездок_в_день).

![визуализация результата 7](./media/tutorial-data-analyst/7.png)

По графику можно наблюдать недельную периодичность колебаний количества поездок, в которой пик приходится на субботу. В летние месяцы в связи с периодом отпусков число поездок уменьшается. Но есть ряд значительных спадов в количестве поездок на такси, в которых не просматривается какая-либо определенная периодичность и причина которых непонятна.

Теперь давайте посмотрим, можно ли связать эти спады с государственными праздниками, объединив набор данных такси Нью-Йорка с набором данных о государственных праздниках:

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

![визуализация результата 8](./media/tutorial-data-analyst/8.png)

На этот раз мы хотим выделить число поездок на такси в дни государственных праздников. Для этого мы выберем значение "none" (нет) для столбца категории, а для столбцов условных обозначений (ряды) — "rides_per_day" (поездок_в_день) и "holiday" (праздник).

![визуализация результата 9](./media/tutorial-data-analyst/9.png)

По графику четко видно, что в дни государственных праздников количество поездок на такси уменьшается. Но по-прежнему остается необъясненным огромный спад 23 января. Давайте проверим погоду в Нью-Йорке в этот день, запросив набор данных о погоде:

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

![визуализация результата 10](./media/tutorial-data-analyst/10.png)

Результаты запроса указывают на то, что уменьшение количества поездок на такси произошло по следующим причинам:

- в этот день в Нью-Йорке была метель, так как выпало большое количество снега (около 30 см);
- было холодно (температура ниже нуля по Цельсию);
- было ветрено (скорость ветра около 10 м/с).

В этом руководстве показано, как специалист по анализу данных может быстро выполнять исследовательский анализ данных, легко объединять различные наборы данных с использованием службы SQL по запросу и визуализировать результаты с помощью Azure Synapse Studio.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [этой статьей](tutorial-connect-power-bi-desktop.md), чтобы узнать, как подключить службу SQL по запросу к Power BI Desktop и создавать отчеты.
 
