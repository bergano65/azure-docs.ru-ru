---
title: Макеты паркета с помощью S'L по требованию (предварительный просмотр)
description: В этой статье вы узнаете, как задать запрос паркет ныхгнездяных типов.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431661"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>В Azure Synapse Analytics пристежочные типы паркета с помощью S'L по требованию

В этой статье вы узнаете, как написать запрос с помощью S'L по требованию (предварительный просмотр) в Azure Synapse Analytics.  Этот запрос будет читать паркет вложенных типов.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать остальную часть этой статьи, просмотрите следующие статьи:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Проект вложенных или повторяющихся данных

Следующий запрос читает *файл justSimpleArray.parquet.* Он проецирует все столбцы из файла Паркета, включая вложенные или повторяющиеся данные.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Элементы доступа из вложенных столбцов

Следующий запрос читает файл *structExample.parquet* и показывает, как всплывать элементы вложенного столбца:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Элементы доступа из повторяющихся столбцов

Следующий запрос читает файл *justSimpleArray.parquet* и использует [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для извлечения **элемента из** повторяющихся столбцов, таких как Array или Map:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

Следующий запрос читает файл *mapExample.parquet* и использует [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для извлечения элемента, не связанного с **масштабами,** из повторяющихся столбцов, таких как Array или Map:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Дальнейшие действия

В следующей статье будет показан о том, как [запрос JSON файлов](query-json-files.md).
