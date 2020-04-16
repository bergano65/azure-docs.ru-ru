---
title: Использование метаданных файлов в запросах
description: Функция OPENROWSET предоставляет информацию о файлах и путях каждого файла, используемого в запросе для фильтрации или анализа данных на основе имени файла и/или пути папки.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431557"
---
# <a name="using-file-metadata-in-queries"></a>Использование метаданных файлов в запросах

Служба запросов по запросу S'L может адресовать несколько файлов и папок, как описано в [папках запросов и](query-folders-multiple-csv-files.md) статье нескольких файлов. В этой статье вы узнаете, как использовать информацию о метаданных об именах файлов и папок в запросах.

Иногда может потребоваться знать, какой источник файла или папки коррелирует с определенной строкой в наборе результатов.

Вы можете `filepath` использовать `filename` функцию и возвращать имена файлов и/или путь в наборе результатов. Или вы можете использовать их для фильтрации данных на основе имени файла и/или пути папки. Эти функции описаны в [функции имени файла](develop-storage-files-overview.md#filename-function) синтаксиса и [функции filepath.](develop-storage-files-overview.md#filepath-function) Ниже вы найдете краткое описание вдоль образцов.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем прочитать остальную часть этой статьи, просмотрите следующие предпосылки:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="functions"></a>Функции

### <a name="filename"></a>имя_файла

Эта функция возвращает имя файла, из которого исходит строка.

В следующем примере считывается NYC Yellow Taxi файлы данных за последние три месяца 2017 года и возвращает количество поездок на файл. В части запроса OPENROWSET указывается, какие файлы будут прочитаны.

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

Ниже приводится, как *имя файла ()* может быть использовано в пункте WHERE для фильтрации прочитанных файлов. Он получает доступ ко всей папке в части запроса OPENROWSET и фильтрует файлы в пункте WHERE.

Ваши результаты будут такими же, как и в предыдущем примере.

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

### <a name="filepath"></a>Filepath

Функция файлопата возвращает полный или частичный путь:

- При вызове без параметра он возвращает полный путь файла, из которого исходит строка.
- При вызове с параметром он возвращает часть пути, который соответствует подстановочному знаку на позиции, указанной в параметре. Например, значение параметра 1 возвращает часть пути, которая соответствует первому подстановке.

В следующем примере считывается NYC Yellow Taxi файлы данных за последние три месяца 2017 года. Он возвращает количество поездок на траекторию файла. В части запроса OPENROWSET указывается, какие файлы будут прочитаны.

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

Ниже приводится пример, как *файла()* может быть использован в оговорке WHERE для фильтрации файлов для чтения.

Вы можете использовать подстановочные карты в части запроса OPENROWSET и отфильтровать файлы в пункте WHERE. Ваши результаты будут такими же, как и в предыдущем примере.

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

## <a name="next-steps"></a>Дальнейшие действия

В следующей статье вы узнаете, как [задать запрос файлов Паркета](query-parquet-files.md).
