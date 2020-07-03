---
title: Использование метаданных файлов в запросах
description: Функция OPENROWSET предоставляет сведения о файле и пути о каждом файле, используемом в запросе, для фильтрации или анализа данных по имени файла и (или) пути к папке.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431557"
---
# <a name="using-file-metadata-in-queries"></a>Использование метаданных файлов в запросах

Служба запросов по запросу SQL может обращаться к нескольким файлам и папкам, как описано в статье о [папках запросов и нескольких файлах](query-folders-multiple-csv-files.md) . В этой статье вы узнаете, как использовать метаданные о именах файлов и папок в запросах.

Иногда может потребоваться знать, какой источник файла или папки соотносится с определенной строкой результирующего набора.

Можно использовать функцию `filepath` и `filename` для возврата имен файлов и (или) пути в результирующий набор. Их также можно использовать для фильтрации данных на основе имени файла и (или) пути к папке. Эти функции описаны в разделе синтаксис [имени файла](develop-storage-files-overview.md#filename-function) и [функция FilePath](develop-storage-files-overview.md#filepath-function). Ниже приведены краткие описания и примеры.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со следующими предварительными требованиями.

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="functions"></a>Функции

### <a name="filename"></a>имя_файла

Эта функция возвращает имя файла, являющегося источником строки.

Следующий пример считывает файлы данных Нью желтого такси за последние три месяца 2017 и возвращает число переданных на файл. Часть инструкции OPENROWSET в запросе указывает, какие файлы будут считаны.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

В следующем примере показано, как можно использовать *filename ()* в предложении WHERE для фильтрации считываемых файлов. Он обращается ко всей папке в части OPENROWSET файлов запроса и фильтрует в предложении WHERE.

Результаты будут такими же, как в предыдущем примере.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Равно

Функция FilePath возвращает полный или частичный путь:

- При вызове без параметра возвращается полный путь к файлу, из которого получена строка.
- При вызове с параметром он возвращает часть пути, соответствующую подстановочному знаку в положении, указанном в параметре. Например, значение параметра 1 вернет часть пути, совпадающую с первым подстановочным знаком.

В следующем примере считываются файлы данных Нью желтого такси за последние три месяца 2017. Он возвращает число переданных на путь к файлу. Часть инструкции OPENROWSET в запросе указывает, какие файлы будут считаны.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

В следующем примере показано, как можно использовать *FilePath ()* в предложении WHERE для фильтрации считываемых файлов.

Можно использовать подстановочные знаки в части OPENROWSET запроса и фильтровать файлы в предложении WHERE. Результаты будут такими же, как в предыдущем примере.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Дальнейшие шаги

В следующей статье вы узнаете, как [запрашивать файлы Parquet](query-parquet-files.md).
