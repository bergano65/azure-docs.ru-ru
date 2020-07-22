---
title: Запрос файлов Parquet с помощью SQL по запросу (предварительная версия)
description: Из этой статьи вы узнаете, как запрашивать файлы Parquet, используя решение "SQL по запросу" (предварительная версия).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207572"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос файлов Parquet с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics

В этой статье вы узнаете, как с помощью SQL по запросу (предварительная версия) написать запрос, который будет считывать файлы Parquet.

## <a name="prerequisites"></a>Предварительные требования

Первым делом вам нужно **создать базу данных**, у которой источник данных ссылается на учетную запись хранения [набора NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Затем инициализируйте объекты, выполнив [скрипт настройки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот сценарий установки создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются в этих примерах.

## <a name="dataset"></a>Dataset

В этом примере используется набор данных [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Запросить файлы Parquet можно таким же образом, как вы [считываете CSV-файлы](query-parquet-files.md). Единственное различие состоит в том, что параметр `FILEFORMAT` должен иметь значение `PARQUET`. В примерах в этой статье показаны особенности считывания файлов Parquet.

## <a name="query-set-of-parquet-files"></a>Запрашивание набора файлов Parquet

При запросе файлов Parquet можно указать только необходимые столбцы.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Автоматический вывод схемы

При чтении файлов Parquet не нужно использовать предложение OPENROWSET WITH. Имена столбцов и типы данных автоматически считываются из файлов Parquet.

В приведенном ниже примере показаны возможности автоматического вывода схемы для файлов Parquet. Он возвращает число строк за сентябрь 2017 г. без указания схемы.

> [!NOTE]
> При чтении файлов Parquet указывать столбцы в предложении OPENROWSET WITH не требуется. В этом случае служба запросов SQL по запросу будет использовать метаданные в файле Parquet и привязывать столбцы по имени.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Запрашивание секционированных данных

Набор данных, представленный в этом примере, разделен на отдельные вложенные папки. Вы можете выбрать конкретные разделы с помощью функции filepath. В этом примере показаны суммы по тарифам за год, месяц и payment_type за первые три месяца 2017 года.

> [!NOTE]
> Запрос службы "SQL по запросу" совместим со схемой секционирования Hive на платформе Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Сопоставление типов

Файлы Parquet содержат описания типов для каждого столбца. В приведенной ниже таблице показано, как типы Parquet сопоставляются с собственными типами SQL.

| Тип Parquet | Логический тип Parquet (заметка) | Тип данных SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(параметры сортировки UTF8) |
| BINARY |STRING |varchar \*(параметры сортировки UTF8) |
| BINARY |ENUM|varchar \*(параметры сортировки UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar(max) \*(параметры сортировки UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar(max), сериализованный в стандартизированный формат |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |BIGINT |
| INT32 |DATE |Дата |
| INT32 |DECIMAL |Decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |BIGINT |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |Decimal |
| INT64 |TIME (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Список |varchar(max), сериализованный в JSON |
|[Сложный тип](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), сериализованный в JSON |

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующей статье, чтобы узнать, как [запросить вложенные типы Parquet](query-parquet-nested-types.md).
