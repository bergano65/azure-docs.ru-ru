---
title: Сохранение результатов запроса в хранилище
description: Из этой статьи вы узнаете, как сохранять результаты запросов в хранилище, используя решение "SQL по запросу" (предварительная версия).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691867"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Сохранение результатов запросов в хранилище с помощью решения "SQL по запросу" (предварительная версия) с помощью Azure Synapse Analytics

Из этой статьи вы узнаете, как сохранять результаты запросов в хранилище, используя решение "SQL по запросу" (предварительная версия).

## <a name="prerequisites"></a>Предварительные требования

Сначала необходимо ознакомиться со статьями, приведенными ниже, и убедиться, что выполнены необходимые условия:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

Чтобы сохранить результаты запроса в хранилище, можно использовать инструкцию CREATE EXTERNAL TABLE AS SELECT (CETAS).

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup). Вам необходимо изменить параметр LOCATION внешнего источника данных MyDataSource, чтобы оно указывало на расположение, для которого у вас есть разрешение на запись. 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Использование созданной внешней таблицы

Вы можете использовать внешнюю таблицу, созданную с помощью CETAS, как обычную внешнюю таблицу.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как запрашивать различные типы файлов, см. в статьях [Запрашивание одного CSV-файла](query-single-csv-file.md), [Запрашивание файлов Parquet](query-parquet-files.md) и [Запрашивание файлов JSON](query-json-files.md).
