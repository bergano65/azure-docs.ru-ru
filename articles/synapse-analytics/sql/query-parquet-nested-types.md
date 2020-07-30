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
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386611"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос вложенных типов в файлах Parquet и JSON с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics. Этот запрос будет считывать вложенные типы Parquet.
Вложенные типы являются сложными структурами, представляющими объекты или массивы. Вложенные типы могут храниться в: 
- [Parquet](query-parquet-files.md) , где можно иметь несколько сложных столбцов, содержащих массивы и объекты.
- Иерархические [файлы JSON](query-json-files.md) , в которых можно читать СЛОЖНЫЕ документы JSON в виде одного столбца.
- Коллекция CosmosDB, в которой каждый документ может содержать сложные вложенные свойства (в настоящее время в общедоступной предварительной версии).

Синапсе SQL по запросу форматирует все вложенные типы как объекты JSON и массивы, поэтому вы можете [извлекать или изменять сложные объекты с помощью функций JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) или [анализировать данные JSON с помощью функции OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Ниже приведен один пример запроса, который извлекает скалярные значения и из [ковид-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON file с вложенными объектами. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`функция возвращает скалярное значение из поля по указанному пути. `JSON_QUERY`функция возвращает объект, отформатированный в формате JSON, из поля по указанному пути.

> [!IMPORTANT]
> В этом примере используется файл из [ковид-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). См. раздел лицензия этого и структура данных на этой странице.

## <a name="prerequisites"></a>Предварительные требования

Сначала нужно **создать базу данных**, у которой источник данных имеет ссылку. Затем инициализируйте объекты, выполнив [сценарий настройки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот сценарий настройки создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются в этих примерах.

## <a name="project-nested-or-repeated-data"></a>Вложенные или повторяющиеся данные в проекте

Файл PARQUET может иметь несколько столбцов со сложными типами. Значения из этих столбцов форматируются в виде текста JSON и возвращаются в виде столбца VARCHAR. Следующий запрос считывает файл *структексампле. Parquet* и показывает, как считывать значения вложенных столбцов: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Этот запрос возвращает следующий результат, в котором содержимое каждого вложенного объекта возвращается в виде текста JSON:

| датеструкт    | тиместрукт    | тиместампструкт   | деЦималструкт | флоатструкт |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Приведенный ниже запрос считывает файл *justSimpleArray.parquet*. Он проецирует все столбцы из файла Parquet, включая вложенные или повторяющиеся данные.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Этот запрос возвратит следующий результат:

|симплеаррай|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Чтение свойств из столбцов вложенных объектов

`JSON_VALUE`функция позволяет возвращать значения из столбца, отформатированного в виде текста JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Результат показан в следующей таблице:

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Дополнительная информация — епидемиоло... | Жюльен   | -Рис. S1: Филожени из... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

В отличие от файлов JSON, которые в большинстве случаев возвращают один столбец, содержащий сложный объект JSON. Файлы PARQUET могут иметь несколько сложностей. Можно считывать свойства вложенного столбца с помощью `JSON_VALUE` функции для каждого столбца. `OPENROWSET`позволяет непосредственно указывать пути вложенных свойств в `WITH` предложении. Пути можно задать в качестве имени столбца или можно добавить [выражение пути JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) после типа столбца.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
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

Результат показан в следующей таблице:

|симплеаррай    | фирстелемент  | секонделемент | сирделемент |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Доступ к подобъектам из сложных столбцов

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

Структура `MapOfPersons` возвращается в виде `VARCHAR` столбца и отформатирована как строка JSON.

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
