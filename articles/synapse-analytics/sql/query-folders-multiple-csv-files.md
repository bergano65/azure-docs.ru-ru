---
title: Папки запросов и несколько файлов CSV, использующих S'L по требованию (предварительный просмотр)
description: СЗЛ по требованию (предварительный просмотр) поддерживает чтение нескольких файлов/ папок с помощью подстановочных знаков, которые аналогичны подстановочным знакам, используемым в ОС Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457371"
---
# <a name="query-folders-and-multiple-csv-files"></a>Папки запросов и несколько файлов CSV  

В этой статье вы узнаете, как написать запрос с помощью S'L по требованию (предварительный просмотр) в Azure Synapse Analytics.

По требованию S'L поддерживает чтение нескольких файлов/папок с помощью подстановочных знаков, которые аналогичны подстановочным знакам, используемым в ОС Сот-Ки. Тем не менее, большая гибкость присутствует, поскольку несколько подстановочных знаков разрешено.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать остальную часть этой статьи, не забудьте просмотреть статьи, перечисленные ниже:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Читать несколько файлов в папке

Вы будете использовать folder *csv/taxi* для выполнения выборочных запросов. Он содержит данные NYC Taxi - Yellow Taxi Trip Records с июля 2016 года по июнь 2018 года.

Файлы в *csv/taxi* названы по году и месяцу:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Каждый файл имеет следующую структуру:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Читать все файлы в папке
    
Приведенный ниже пример читает все файлы данных NYC Yellow Taxi из папки *csv/taxi* и возвращает общее количество пассажиров и поездок в год. Он также показывает использование агрегированных функций.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Все файлы, доступ к которых имеет сяртовую систему OPENROWSET, должны иметь одинаковую структуру (т.е. количество столбцов и типы их данных).

### <a name="read-subset-of-files-in-folder"></a>Чтение подмножества файлов в папке

Приведенный ниже пример читает файлы данных NYC Yellow Taxi за 2017 год из папки *csv/taxi* с помощью подстановочного знака и возвращает общую сумму тарифа за тип платежа.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Все файлы, доступ к которых имеет сяртовую систему OPENROWSET, должны иметь одинаковую структуру (т.е. количество столбцов и типы их данных).

## <a name="read-folders"></a>Читать папки

Путь, который вы предоставляете OPENROWSET, также может быть путем к папке. Следующие разделы включают эти типы запросов.

### <a name="read-all-files-from-specific-folder"></a>Читать все файлы из конкретной папки

Вы можете прочитать все файлы в папке, используя подстановочный знак уровня файла, как показано в [Прочитайте все файлы в папке.](#read-all-files-in-folder) Но есть способ заставить папку и использовать все файлы в этой папке.

Если путь, представленный в OPENROWSET, указывает на папку, все файлы в этой папке будут использоваться в качестве источника для запроса. Следующий запрос будет читать все файлы в папке *csv/taxi.*

> [!NOTE]
> Обратите внимание на существование / в конце пути в запросе ниже. Он обозначает папку. Если / опущен, запрос будет направлен на файл под названием *такси* вместо.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Все файлы, доступ к которых имеет сяртовую систему OPENROWSET, должны иметь одинаковую структуру (т.е. количество столбцов и типы их данных).

### <a name="read-all-files-from-multiple-folders"></a>Читать все файлы из нескольких папок

Можно читать файлы из нескольких папок с помощью подстановочного знака. Следующий запрос будет читать все файлы из всех папок, расположенных в папке *csv,* которые имеют имена, начиная с *т* и заканчивая *i*.

> [!NOTE]
> Обратите внимание на существование / в конце пути в запросе ниже. Он обозначает папку. Если / опущен, запрос будет нацелен на файлы, названные *т&ast;я* вместо.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Все файлы, доступ к которых имеет сяртовую систему OPENROWSET, должны иметь одинаковую структуру (т.е. количество столбцов и типы их данных).

Поскольку у вас есть только одна папка, которая соответствует критериям, результат запроса такой же, как [Прочитайте все файлы в папке.](#read-all-files-in-folder)

## <a name="multiple-wildcards"></a>Несколько подстановочных знаков

Вы можете использовать несколько подстановочных знаков на разных уровнях пути. Например, можно обогатить предыдущий запрос, чтобы прочитать файлы только с данными 2017 года, из всех папок, имена которых начинаются с *t* и заканчиваются *i.*

> [!NOTE]
> Обратите внимание на существование / в конце пути в запросе ниже. Он обозначает папку. Если / опущен, запрос будет нацелен на файлы, названные *т&ast;я* вместо.
> Существует максимальный лимит 10 подстановочных знаков за запрос.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Все файлы, доступ к которых имеет сяртовую систему OPENROWSET, должны иметь одинаковую структуру (т.е. количество столбцов и типы их данных).

Поскольку у вас есть только одна папка, которая соответствует критериям, результат запроса такой же, как [Чтение подмножество файлов в папке](#read-subset-of-files-in-folder) и [Прочитайте все файлы из конкретной папки.](#read-all-files-from-specific-folder) Более сложные сценарии использования подстановочных знаков описаны в [файлах Query Parquet.](query-parquet-files.md)

## <a name="next-steps"></a>Следующие шаги

Более подробную информацию можно найти в статье [в конкретных файлах запроса.](query-specific-files.md)
