---
title: Запрос файлов Parquet с помощью SQL по запросу (Предварительная версия)
description: В этой статье вы узнаете, как запрашивать файлы Parquet с помощью SQL по запросу (Предварительная версия).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431700"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос файлов Parquet с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (Предварительная версия), который будет считывать файлы Parquet.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со следующими статьями:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Вы можете запросить файлы Parquet так же, как и CSV-файлы. Единственное отличие заключается в том, что параметр FILEFORMAT должен иметь значение PARQUET. В примерах этой статьи показаны особенности чтения файлов Parquet.

> [!NOTE]
> При чтении файлов Parquet указывать столбцы в предложении OPENROWSET WITH не требуется. SQL по запросу будет использовать метаданные в файле Parquet и привязывать столбцы по имени.

Для образцов запросов вы будете использовать папку *Parquet/такси* . Он содержит данные о Нью такси-желтом путешествии, которые записываются с 2016 июля. до июня 2018.

Данные секционированы по годам и месяцам, а структура папок выглядит следующим образом:

- год = 2016
  - месяц = 6
  - ...
  - месяц = 12
- year = 2017 г.
  - месяц = 1
  - ...
  - месяц = 12
- year = 2018
  - месяц = 1
  - ...
  - месяц = 6

## <a name="query-set-of-parquet-files"></a>Набор запросов Parquet файлов

При запросе файлов Parquet можно указать только интересующие столбцы.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Автоматический вывод схемы

При чтении файлов Parquet не нужно использовать предложение OPENROWSET WITH. Имена столбцов и типов данных автоматически считываются из файлов Parquet.

В следующем примере показаны возможности автоматического вывода схемы для файлов Parquet. Он возвращает число строк в сентябре 2017 без указания схемы.

> [!NOTE]
> При чтении файлов Parquet не нужно указывать столбцы в предложении OPENROWSET WITH. В этом случае служба запросов по запросу SQL будет использовать метаданные в файле Parquet и привязывать столбцы по имени.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Запрос секционированных данных

Набор данных, указанный в этом примере, разделен на отдельные вложенные папки. Вы можете ориентироваться на конкретные секции с помощью функции FilePath. В этом примере показаны FARE суммы по годам, месяцам и payment_type за первые три месяца 2017.

> [!NOTE]
> Запрос SQL по запросу совместим со схемой секционирования Hive/Hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Сопоставление типов

Файлы Parquet содержат описания типов для каждого столбца. В следующей таблице описано, как типы Parquet сопоставляются с собственными типами SQL.

| Тип Parquet | Логический тип Parquet (Аннотация) | Тип данных SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| ДВОИЧНЫЙ/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| ТИПА | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(параметры сортировки UTF8) |
| BINARY |STRING |varchar \*(параметры сортировки UTF8) |
| BINARY |ПЕРЕЧИСЛЕНИЯ|varchar \*(параметры сортировки UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar (max) \*(параметры сортировки UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), сериализованный в стандартизированный формат |
| ТИПА |INT (8, true) |smallint |
| ТИПА |INT (16, true) |smallint |
| ТИПА |INT (32, true) |INT |
| ТИПА |INT (8, false) |tinyint |
| ТИПА |INT (16, false) |INT |
| ТИПА |INT (32, false) |BIGINT |
| ТИПА |DATE |Дата |
| ТИПА |DECIMAL |Decimal |
| ТИПА |время ()|time |
| INT64 |INT (64, true) |BIGINT |
| INT64 |INT (64, false) |десятичное число (20, 0) |
| INT64 |DECIMAL |Decimal |
| INT64 |ВРЕМЯ (МИКРОСРЕДЫ И NANO) |time |
|INT64 |МЕТКА ВРЕМЕНИ (НА«НА», МИКРОИЛИ NANOS) |datetime2 |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Список |varchar (max), сериализованный в JSON |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), сериализованный в JSON |

## <a name="next-steps"></a>Дальнейшие шаги

Перейдите к следующей статье, чтобы узнать, как [запросить вложенные типы Parquet](query-parquet-nested-types.md).
