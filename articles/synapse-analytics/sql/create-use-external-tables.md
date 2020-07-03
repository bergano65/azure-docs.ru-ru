---
title: Создание и использование внешних таблиц в SQL по запросу (предварительная версия)
description: В этом разделе вы узнаете, как создавать и использовать внешние таблицы SQL по запросу (предварительная версия). Внешние таблицы полезны, если требуется управлять доступом к внешним данным в SQL по запросу и использовать такие средства, как Power BI, в сочетании с SQL по запросу.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420798"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Создание и использование внешних таблиц в SQL по запросу (предварительная версия) с помощью Azure Synapse Analytics

В этом разделе вы узнаете, как создавать и использовать внешние таблицы SQL по запросу (предварительная версия). Внешние таблицы полезны, если требуется управлять доступом к внешним данным в SQL по запросу и использовать такие средства, как Power BI, в сочетании с SQL по запросу.

## <a name="prerequisites"></a>Предварительные требования

Первым делом необходимо ознакомиться со статьями, приведенными ниже, и убедиться, что выполнены необходимые условия для создания и использования внешних таблиц SQL по запросу:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Создание внешней таблицы

Внешние таблицы можно создавать так же, как и обычные внешние таблицы SQL Server. Приведенный ниже запрос создает внешнюю таблицу, которая считывает файл *population.csv*.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Использование внешней таблицы

Внешние таблицы в запросах можно использовать так же, как и в запросах SQL Server.

Следующий запрос демонстрирует использование внешней таблицы *population*, созданной в разделе [Создание внешней таблицы](#create-an-external-table). Он возвращает названия стран с их населением в 2019 г. в убывающем порядке.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в статье [Сохранение результатов запроса в хранилище при использовании SQL по запросу (предварительная версия) и Azure Synapse Analytics](../sql/create-external-table-as-select.md).
