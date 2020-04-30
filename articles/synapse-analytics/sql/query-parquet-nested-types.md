---
title: Запрос Parquet вложенных типов с помощью SQL по запросу (Предварительная версия)
description: В этой статье вы узнаете, как запросить вложенные типы Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431661"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос Parquet вложенных типов с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics.  Этот запрос будет считывать вложенные типы Parquet.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со следующими статьями:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Вложенные или повторяющиеся данные в проекте

Следующий запрос считывает файл *жустсимплеаррай. Parquet* . Он проецирует все столбцы из файла Parquet, включая вложенные или повторяющиеся данные.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Доступ к элементам из вложенных столбцов

Следующий запрос считывает файл *структексампле. Parquet* и показывает, как Surface Elements вложенного столбца:

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

## <a name="access-elements-from-repeated-columns"></a>Доступ к элементам из повторяющихся столбцов

Следующий запрос считывает файл *жустсимплеаррай. Parquet* и использует [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения **скалярного** элемента из повторяющегося столбца, например массива или Map:

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

Следующий запрос считывает файл *мапексампле. Parquet* и использует [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения **нескалярного** элемента из повторяющегося столбца, например массива или Map:

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

## <a name="next-steps"></a>Дальнейшие шаги

В следующей статье будет показано, как выполнять [запросы к ФАЙЛАМ JSON](query-json-files.md).
