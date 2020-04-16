---
title: Файлы паркета запроса с использованием S'L по требованию (предварительный просмотр)
description: В этой статье вы узнаете, как задать запрос файлов Паркета с помощью S'L по требованию (предварительный просмотр).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431700"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Файлы «Паркет ный образец» с использованием S'L по требованию (предварительный просмотр) в Azure Synapse Analytics

В этой статье вы узнаете, как написать запрос, используя S'L по требованию (предварительный просмотр), который будет читать файлы Паркета.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать остальную часть этой статьи, просмотрите следующие статьи:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Вы можете загонять файлы Паркета так же, как вы читаете файлы CSV. Разница лишь в том, что параметр FILEFORMAT должен быть установлен на ПАРКЕТ. Примеры в этой статье показывают специфику чтения файлов паркета.

> [!NOTE]
> При чтении паркетных файлов не нужно указывать столбцы в пункте OPENROWSET WITH. По требованию S'L будет использовать метаданные в файле Паркета и связывать столбцы по имени.

Вы будете использовать *паркет/такси* для примера запросов. Он содержит данные NYC Taxi - Yellow Taxi Trip Records за июль 2016 года. до июня 2018 года.

Данные разделены по годам и месяцам, а структура папок такова:

- 2016 год
  - месяц No6
  - ...
  - месяц No12
- 2017 год
  - месяц No1
  - ...
  - месяц No12
- 2018 год
  - месяц No1
  - ...
  - месяц No6

## <a name="query-set-of-parquet-files"></a>Набор файлов паркета

Вы можете указать только столбцы, представляющие интерес, при запросе файлов Паркета.

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

## <a name="automatic-schema-inference"></a>Вывод автоматической схемы

Вам не нужно использовать оговорку OPENROWSET WITH при чтении файлов паркета. Имена столбцов и типы данных автоматически считываются из файлов Паркета.

Приведенный ниже пример показывает возможности автоматического вывода схемы для файлов Parquet. Он возвращает количество строк в сентябре 2017 года без указания схемы.

> [!NOTE]
> При чтении файлов паркета не нужно указывать столбцы в пункте OPENROWSET WITH. В этом случае служба запроса по требованию s'L будет использовать метаданные в файле Паркета и связывать столбцы по имени.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Переразделенные данные запроса

Набор данных, представленный в этой выборке, делится (разделен) на отдельные подфолы. Вы можете настроить таргетинг на определенные разделы с помощью функции файлопата. В этом примере показаны суммы тарифов по годам, месяцам и payment_type за первые три месяца 2017 года.

> [!NOTE]
> Запрос по требованию спомощьами совместим со схемой раздела Hive/Hadoop.

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

Файлы паркета содержат описания типов для каждого столбца. В следующей таблице описывается, как типы паркета отображаются на типах z'L.

| Тип паркета | Паркет ный тип (аннотация) | Тип данных SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(сопоставление UTF8) |
| BINARY |STRING |varchar \*(сопоставление UTF8) |
| BINARY |Перечисления|varchar \*(сопоставление UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar (max) \*(коллаж UTF8) |
| BINARY |Bson |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), сериализованный в стандартизированный формат |
| INT32 |INT (8, правда) |smallint |
| INT32 |INT (16, правда) |smallint |
| INT32 |INT (32, правда) |INT |
| INT32 |INT (8, ложные) |tinyint |
| INT32 |INT (16, ложный) |INT |
| INT32 |INT (32, ложные) |BIGINT |
| INT32 |DATE |Дата |
| INT32 |DECIMAL |Decimal |
| INT32 |ВРЕМЯ (MILLIS )|time |
| INT64 |INT (64, правда) |BIGINT |
| INT64 |INT (64, ложные ) |десятичная (20,0) |
| INT64 |DECIMAL |Decimal |
| INT64 |ВРЕМЯ (MICROS / NANOS) |time |
|INT64 |ВРЕМЯТАМ (МИЛЛИС / MICROS / NANOS) |datetime2 |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Список |varchar (max), сериализованный в JSON |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), сериализованный в JSON |

## <a name="next-steps"></a>Дальнейшие действия

Заранее к следующей статье, чтобы узнать, как [запрос паркет вложенных типов](query-parquet-nested-types.md).
