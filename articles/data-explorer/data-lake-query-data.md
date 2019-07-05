---
title: Запросы к данным в Azure Data Lake с помощью обозревателя данных Azure
description: Узнайте, как запрашивать данные в Azure Data Lake с помощью обозревателя данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453181"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Запросы к данным в Azure Data Lake с помощью обозревателя данных Azure (Предварительная версия)

Хранилище Azure Data Lake — это решение Озера данных с высокой масштабируемостью и экономичное для анализа больших данных. Оно сочетает в себе мощь высокопроизводительной файловой системы массивного масштабирования и экономии, что помогает сократить время анализа данных. Data Lake Storage Gen2 расширяет возможности хранилища BLOB-объектов Azure и является оптимизированным для рабочих нагрузок аналитики.
 
Обозреватель данных Azure интегрируется со службой хранилища BLOB-объектов Azure и Azure данных Озера хранилища поколения 2, fast, предоставляя кэшируются и индексированного доступа к данным в Data lake. Вы можете анализировать и запрашивать данными в озере без предыдущего приема в обозреватель данных Azure. Также можно запрашивать полученные и uningested собственного Озера данных одновременно.  

> [!TIP]
> Оптимальную производительность запросов требуется открытое прием данных в обозреватель данных Azure. Возможность запрашивать данные в Gen2 хранилища Озера данных Azure без предыдущего приема должен использоваться только для исторических данных или данные, которые запрашиваются редко.
 
## <a name="optimize-query-performance-in-the-lake"></a>Оптимизация производительности запросов в озере 

* Секции данных для повышения производительности и времени оптимизированных запросов.
* Сжатие данных для повышения производительности (чтобы обеспечить лучшее сжатие gzip, lz4 для достижения оптимальной производительности).
* Использование хранилища BLOB-объектов Azure или Gen2 хранилища Озера данных Azure с помощью той же области, что и кластер обозреватель данных Azure. 

## <a name="create-an-external-table"></a>Создание внешней таблицы

1. Используйте `.create external table` команду, чтобы создать внешнюю таблицу в обозревателе данных Azure. Дополнительная таблица внешней команды, такие как `.show`, `.drop`, и `.alter` описаны в [внешней таблицы команд](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Этот запрос создает ежедневных *container1/yyyy/MM/dd/all_exported_blobs.csv*. Повышенная производительность ожидается более детализированные секционирование. Например запросы к внешним таблицам с ежедневной разделов, например выше, будет иметь лучшую производительность, чем эти запросы с помощью ежемесячных секционированных таблиц.

    > [!NOTE]
    > В настоящее время поддерживаемых учетных записей хранения являются хранилище BLOB-объектов или Gen2 хранилища Озера данных Azure. В настоящее время поддерживаемые форматы данных: csv, tsv и txt.

1. Внешняя таблица отображается в левой части веб-Интерфейс

    ![Внешняя таблица, в веб-Интерфейсе](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Разрешения внешней таблицы
 
* Пользователь базы данных можно создать внешнюю таблицу. Создатель таблицы автоматически становится администратором таблицы.
* Кластер, базы данных или таблицы администратора можно изменить существующую таблицу.
* Любой пользователь базы данных или модуль чтения может запрашивать внешней таблицы.
 
## <a name="query-an-external-table"></a>Запрашивании внешней таблицы
 
Чтобы запросить внешнюю таблицу, используйте `external_table()` функции и укажите имя таблицы в качестве аргумента функции. Остальная часть запроса — стандартный язык запросов Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense не поддерживается в настоящее время на запросах внешней таблицы.

## <a name="query-external-and-ingested-data-together"></a>Запрашивать данные внешнего и полученные вместе

Вы можете запрашивать внешние таблицы и таблицы данные, полученные в рамках того же запроса. Вы [ `join` ](/azure/kusto/query/joinoperator) или [ `union` ](/azure/kusto/query/unionoperator) внешней таблицы с использованием дополнительных данных из обозревателя данных Azure, серверов SQL Server или других источников. Используйте [ `let( ) statement` ](/azure/kusto/query/letstatement) присваиваемое сокращенным именем ссылки на внешнюю таблицу.

В следующем примере *продуктов* представляет собой таблицу данные, полученные и *ArchivedProducts* имеет внешнюю таблицу, которая содержит данные в Gen2 хранилища Озера данных Azure:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Запрос *TaxiRides* внешней таблицы в кластере

*TaxiRides* пример набора данных содержит данные о поездках в такси Нью-Йорка из [такси Нью-ЙОРКА и Лимузинов комиссии](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Создание внешней таблицы *TaxiRides* 

> [!NOTE]
> В этом разделе показан запрос, используемый для создания *TaxiRides* внешней таблицы в *помочь* кластера. Так как уже существует в этой таблице можно пропустить этот раздел и выполнять [запроса *TaxiRides* данных внешней таблицы](#query-taxirides-external-table-data). 

1. Следующий запрос был использован для создания внешней таблицы *TaxiRides* в кластере. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. Результирующая таблица была создана в *помочь* кластера:

    ![TaxiRides внешней таблицы](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Запрос *TaxiRides* данных внешней таблицы 

Войдите в [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) запрос *TaxiRides* внешней таблицы. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Запрос *TaxiRides* внешней таблицы без секционирования

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) во внешней таблице *TaxiRides* для отображения поездок за каждый день недели, ко всему набору данных. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Этот запрос показывает самых загруженных день недели. Так как данные не секционирована, этот запрос может занять много времени для возврата результатов (до нескольких минут).

![несекционированный запрос визуализации](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Запрос TaxiRides внешней таблицы с секционированием 

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) во внешней таблице *TaxiRides* Отображение типов cab такси (желтый или зеленый) используется в январе 2017 г. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Этот запрос использует секционирование, что позволяет оптимизировать время обработки запроса и производительности. Запрос фильтрует к секционированному столбцу (pickup_datetime) и возвращает результаты через несколько секунд.

![визуализации секционированного запроса](media/data-lake-query-data/taxirides-with-partition.png)
  
Можно создавать дополнительные запросы для выполнения во внешней таблице *TaxiRides* и Дополнительные сведения о данных. 

## <a name="next-steps"></a>Дальнейшие действия

Запрос данных в Azure Data Lake с помощью обозревателя данных Azure. Узнайте, как [писать запросы](write-queries.md) и получать дополнительную информацию из данных.