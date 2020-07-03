---
title: Папки запросов и несколько CSV-файлов с использованием SQL по запросу (Предварительная версия)
description: SQL по запросу (Предварительная версия) поддерживает чтение нескольких файлов и папок с помощью подстановочных знаков, которые похожи на подстановочные знаки, используемые в ОС Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457371"
---
# <a name="query-folders-and-multiple-csv-files"></a>Запрашивание папок и нескольких CSV-файлов  

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics.

SQL по запросу поддерживает чтение нескольких файлов и папок с помощью подстановочных знаков, которые похожи на подстановочные знаки, используемые в ОС Windows. Однако большая гибкость имеется, так как разрешено использование нескольких подстановочных знаков.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со статьями, приведенными ниже:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Чтение нескольких файлов в папке

Для выполнения примеров запросов вы будете использовать *CSV-файл или каталог такси* . В нем содержится Нью такси (желтый), который записывает данные с 2016 июля по 2018 июня.

Файлы в *формате CSV или такси* именуются по годам и месяцу:

- yellow_tripdata_2016 -07. CSV
- yellow_tripdata_2016 -08. CSV
- yellow_tripdata_2016 -09. CSV
- ...
- yellow_tripdata_2018 -04. CSV
- yellow_tripdata_2018 -05. CSV
- yellow_tripdata_2018 -06. CSV

Каждый файл имеет следующую структуру:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Чтение всех файлов в папке
    
В приведенном ниже примере считываются все файлы данных Нью желтого такси из папки *CSV/такси* и возвращается общее число пассажиров и задается в год. Он также показывает использование агрегатных функций.

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
> Все файлы, доступ к которым осуществляется с помощью одной инструкции OPENROWSET, должны иметь одинаковую структуру (т. е. число столбцов и их типы данных).

### <a name="read-subset-of-files-in-folder"></a>Чтение подмножества файлов в папке

В примере ниже считываются файлы данных 2017 Нью желтого такси из папки *CSV/такси* с использованием подстановочных знаков и возвращается общая сумма FARE для каждого типа платежа.

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
> Все файлы, доступ к которым осуществляется с помощью одной инструкции OPENROWSET, должны иметь одинаковую структуру (т. е. число столбцов и их типы данных).

## <a name="read-folders"></a>Чтение папок

Путь, который вы предоставляете в инструкции OPENROWSET, также может быть путем к папке. Эти типы запросов включают следующие разделы.

### <a name="read-all-files-from-specific-folder"></a>Чтение всех файлов из указанной папки

Вы можете читать все файлы в папке, используя подстановочный знак уровня файла, как показано в [статье чтение всех файлов в папке](#read-all-files-in-folder). Но существует способ запросить папку и использовать все файлы в этой папке.

Если путь, указанный в инструкции OPENROWSET, указывает на папку, все файлы в этой папке будут использоваться в качестве источника запроса. Следующий запрос будет считывать все файлы в папке *CSV/такси* .

> [!NOTE]
> Обратите внимание на существование/в конце пути в приведенном ниже запросе. Он обозначает папку. Если параметр/не указан, запрос будет указывать на файл с именем *такси* .

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
> Все файлы, доступ к которым осуществляется с помощью одной инструкции OPENROWSET, должны иметь одинаковую структуру (т. е. число столбцов и их типы данных).

### <a name="read-all-files-from-multiple-folders"></a>Чтение всех файлов из нескольких папок

Можно считывать файлы из нескольких папок с помощью подстановочных знаков. Следующий запрос будет считывать все файлы из всех папок, расположенных в папке *CSV* , имена которых начинаются с *t* и заканчиваются на *i*.

> [!NOTE]
> Обратите внимание на существование/в конце пути в приведенном ниже запросе. Он обозначает папку. Если параметр/не указан, запрос будет указывать на файлы с именем *t&ast;i* .

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
> Все файлы, доступ к которым осуществляется с помощью одной инструкции OPENROWSET, должны иметь одинаковую структуру (т. е. число столбцов и их типы данных).

Так как у вас есть только одна папка, соответствующая критериям, результат запроса будет таким же, как и [чтение всех файлов в папке](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Несколько подстановочных знаков

Можно использовать несколько подстановочных знаков на разных уровнях пути. Например, можно создать предыдущий запрос для чтения файлов только с данными 2017, из всех папок, имена которых начинаются с *t* и заканчиваются на *i*.

> [!NOTE]
> Обратите внимание на существование/в конце пути в приведенном ниже запросе. Он обозначает папку. Если параметр/не указан, запрос будет указывать на файлы с именем *t&ast;i* .
> Максимальное число подстановочных знаков для каждого запроса — 10.

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
> Все файлы, доступ к которым осуществляется с помощью одной инструкции OPENROWSET, должны иметь одинаковую структуру (т. е. число столбцов и их типы данных).

Так как у вас есть только одна папка, соответствующая критериям, результат запроса будет таким же, как и для [чтения подмножества файлов в папке](#read-subset-of-files-in-folder) , и для [чтения всех файлов из указанной папки](#read-all-files-from-specific-folder). Более сложные сценарии использования подстановочных знаков рассматриваются в разделе [файлы Parquet запросов](query-parquet-files.md).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения можно найти в статье о [файлах запроса](query-specific-files.md) .
