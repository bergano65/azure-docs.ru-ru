---
title: Запрос JSON-файлов с помощью SQL по запросу (предварительная версия)
description: В этом разделе объясняется, как читать JSON-файлы с помощью SQL по запросу в Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5d02736e9cb0a612e434dc5a79a73d7a62785728
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207657"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос вложенных типов Parquet с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics. Целью запроса является чтение JSON-файлов. Поддерживаемые форматы перечислены в [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Предварительные требования

Для начала **создайте базу данных**, в которой будут выполняться запросы. Затем инициализируйте объекты, выполнив [скрипт настройки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот сценарий установки создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются в этих примерах.

## <a name="sample-json-files"></a>Примеры JSON-файлов

В разделе ниже приведены примеры сценариев для чтения файлов JSON. Файлы хранятся в контейнере *json*, в папке *Books* и содержат по одной записи книги со следующей структурой:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Чтение файлов JSON

Для обработки JSON-файлов с помощью JSON_VALUE и [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) необходимо прочитать JSON-файл из хранилища в виде одного столбца. Следующий сценарий считывает файл *book1.json* в виде одного столбца:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Выполняется считывание всего JSON-файла в виде одной строки или столбца. Таким образом, FIELDTERMINATOR, FIELDQUOTE и ROWTERMINATOR имеют значение 0x0b.

## <a name="query-json-files-using-json_value"></a>Запрашивайте файлы JSON с помощью JSON_VALUE

В запросе ниже показано, как использовать параметр [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (заголовка, издателя) из книги с заголовком *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics* (Вероятностные и статистические методы в криптологии. Введение по избранным темам):

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Запрашивание файлов JSON с помощью JSON_QUERY

В запросе ниже показано, как использовать параметр [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения объектов и массивов (авторов) из книги с заголовком *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Запрашивание файлов JSON с помощью OPENJSON

В следующем запросе используется [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Он извлечет объекты и свойства в книге, озаглавленной *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Дальнейшие действия

В следующих статьях этой серии показано:

- [Запрашивание папок и нескольких файлов](query-folders-multiple-csv-files.md)
- [Создание и использование представлений](create-use-views.md)
