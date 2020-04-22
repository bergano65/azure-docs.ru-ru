---
title: Создание и использование представлений в SQL по запросу (предварительная версия)
description: В этом разделе вы узнаете, как создавать и использовать представления для создания программы-оболочки SQL по запросу (предварительная версия). Представления позволяют повторно использовать эти запросы. Представления также необходимы, если вы хотите использовать средства, такие как Power BI, в сочетании с SQL по запросу.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420778"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Создание и использование представлений в SQL по запросу (предварительная версия) с помощью Azure Synapse Analytics

В этом разделе вы узнаете, как создавать и использовать представления для создания программы-оболочки SQL по запросу (предварительная версия). Представления позволяют повторно использовать эти запросы. Представления также необходимы, если вы хотите использовать средства, такие как Power BI, в сочетании с SQL по запросу.

## <a name="prerequisites"></a>Предварительные требования

Первым делом необходимо ознакомиться со статьями, приведенными ниже, и убедиться, что выполнены необходимые условия для создания и использования представлений SQL по запросу:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Создание представления

Представления можно создавать так же, как и обычные представления SQL Server. Приведенный ниже запрос создает представление, которое считывает файл *population.csv*.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>Использование представления

Представления в запросах можно использовать так же, как и в запросах SQL Server.

Следующий запрос демонстрирует использование представления *population_csv*, созданного в разделе [Создание представления](#create-a-view). Он возвращает названия стран с их населением в 2019 г. в убывающем порядке.

> [!NOTE]
> Измените первую строку в запросе ([mydbname]), чтобы использовать созданную вами базу данных. Если вы не создали базу данных, ознакомьтесь с разделом [Изначальная настройка](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения о том, как запрашивать различные типы файлов, см. в статьях [Запрашивание одного CSV-файла](query-single-csv-file.md), [Запрашивание файлов Parquet](query-parquet-files.md) и [Запрашивание файлов JSON](query-json-files.md).
