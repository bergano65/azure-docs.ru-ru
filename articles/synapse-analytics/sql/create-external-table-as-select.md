---
title: Сохранение результатов запроса в хранилище
description: Из этой статьи вы узнаете, как сохранять результаты запросов в хранилище, используя решение "SQL по запросу" (предварительная версия).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 610391cefe88f6d066f4af12f6fb88f55b1fe56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206553"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Сохранение результатов запросов в хранилище с помощью решения "SQL по запросу" (предварительная версия) с помощью Azure Synapse Analytics

Из этой статьи вы узнаете, как сохранять результаты запросов в хранилище, используя решение "SQL по запросу" (предварительная версия).

## <a name="prerequisites"></a>Предварительные требования

Для начала **создайте базу данных**, в которой будут выполняться запросы. Затем инициализируйте объекты, выполнив [скрипт настройки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот скрипт создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются для чтения данных в этих примерах.

Придерживаясь инструкций в этой статье, создайте источники данных, учетные данные области базы данных и форматы внешних файлов, которые будут использоваться для записи данных в выходное хранилище.

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

Чтобы сохранить результаты запроса в хранилище, можно использовать инструкцию CREATE EXTERNAL TABLE AS SELECT (CETAS).

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>Использование внешней таблицы

Вы можете использовать внешнюю таблицу, созданную с помощью CETAS, как обычную внешнюю таблицу.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных.

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

Дополнительные сведения о том, как запрашивать различные типы файлов, см. в статьях [Запрашивание одного CSV-файла](query-single-csv-file.md), [Запрашивание файлов Parquet](query-parquet-files.md) и [Запрашивание файлов JSON](query-json-files.md).
