---
title: Запрос JSON-файлов с помощью SQL по запросу (Предварительная версия)
description: В этом разделе объясняется, как читать JSON-файлы с помощью SQL по запросу в Azure синапсе Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197578"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос JSON-файлов с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics. Целью запроса является чтение JSON-файлов. Поддерживаемые форматы перечислены в инструкции [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со следующими статьями:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Примеры файлов JSON

В разделе ниже приведены примеры сценариев для чтения файлов JSON. Файлы хранятся в контейнере *JSON* , в папке *Books*и содержат одну запись книги со следующей структурой:

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

## <a name="read-json-files"></a>Чтение JSON файлов

Для обработки JSON-файлов с помощью JSON_VALUE и [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)необходимо прочитать JSON-файл из хранилища в виде одного столбца. Следующий скрипт считывает файл *book1. JSON* как один столбец:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
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
> Вы читаете весь файл JSON в виде одной строки или столбца. Таким образом, FIELDTERMINATOR, ФИЕЛДКУОТЕ и ROWTERMINATOR имеют значение 0x0B.

## <a name="query-json-files-using-json_value"></a>Запрос JSON файлов с помощью JSON_VALUE

В следующем запросе показано, как использовать [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (Title, Publisher) из книги, озаглавленной *вероятностная, и статистических методов в криптологи, введения по выбранным статьям*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

## <a name="query-json-files-using-json_query"></a>Запрос JSON файлов с помощью JSON_QUERY

В следующем запросе показано, как использовать [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения объектов и массивов (авторов) из книги, озаглавленной *вероятностная, и статистических методов в криптологи, введения по выбранным темам*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

## <a name="query-json-files-using-openjson"></a>Запрос JSON файлов с помощью OPENJSON

В следующем запросе используется [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Он будет извлекать объекты и свойства в книге с правом *вероятностная и статистическими методами в криптологи, а также введением по выбранным статьям*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
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

В следующих статьях этой серии показано, как:

- [Запросы к папкам и нескольким файлам](query-folders-multiple-csv-files.md)
- [Создание и использование представлений](create-use-views.md)
