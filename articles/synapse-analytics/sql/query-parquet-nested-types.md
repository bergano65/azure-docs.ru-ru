---
title: Запрос вложенных типов Parquet с помощью SQL по запросу (предварительная версия)
description: В этой статье описано, как запросить вложенные типы Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bf2dbf501b5cd3b6cd0ab6b0e9bbbc2208c98a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478456"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос вложенных типов Parquet с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics.  Этот запрос будет считывать вложенные типы Parquet.

## <a name="prerequisites"></a>Предварительные требования

Сначала нужно **создать базу данных**, у которой источник данных имеет ссылку. Затем инициализируйте объекты, выполнив [сценарий настройки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот сценарий настройки создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются в этих примерах.

## <a name="project-nested-or-repeated-data"></a>Вложенные или повторяющиеся данные в проекте

Приведенный ниже запрос считывает файл *justSimpleArray.parquet*. Он проецирует все столбцы из файла Parquet, включая вложенные или повторяющиеся данные.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Доступ к элементам из вложенных столбцов

Следующий запрос считывает файл *структексампле. Parquet* и показывает, как Surface Elements вложенного столбца. Существует два способа ссылки на вложенное значение:
- Указание выражения пути вложенного значения после спецификации типа.
- Форматирование имени столбца как вложенного пути с помощью Do "." для ссылки на поля.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Доступ к элементам из повторяющихся столбцов

Следующий запрос считывает файл *justSimpleArray.parquet* и использует [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения **скалярного** элемента из повторяющегося столбца, такого как массив или карта.

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Следующий запрос считывает файл *mapExample.parquet* и использует [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения **нескалярного** элемента из повторяющегося столбца, такого как массив или карта.

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Также можно явно ссылаться на столбцы, которые требуется вернуть в `WITH` предложение:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Структура `MakOfPersons` возвращается в виде `VARCHAR` столбца и отформатирована как строка JSON.

## <a name="projecting-values-from-repeated-columns"></a>Проецирование значений из повторяющихся столбцов

Если имеется массив скалярных значений (например `[1,2,3]` ,) в некоторых столбцах, их можно легко развернуть и соединить с главной строкой, используя следующий скрипт:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Дальнейшие действия

В следующей статье будет показано, как [запрашивать файлы JSON](query-json-files.md).
