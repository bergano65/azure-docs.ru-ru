---
title: Запрос ы JSON файлы с помощью S'L по требованию (предварительный просмотр)
description: В этом разделе рассказывается о том, как читать файлы JSON с помощью S'L по запросу в Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 645baf9102785d223fd1f23ae52a4609725f795b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770808"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Файлы запроса JSON с использованием S'L по требованию (предварительный просмотр) в Azure Synapse Analytics

В этой статье вы узнаете, как написать запрос с помощью S'L по требованию (предварительный просмотр) в Azure Synapse Analytics. Целью запроса является чтение файлов JSON.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать остальную часть этой статьи, просмотрите следующие статьи:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Пример jSON файлов

В разделе ниже содержится пример скриптов для чтения файлов JSON. Файлы хранятся в контейнере *json,* *папках*и содержат одну запись книги со следующей структурой:

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

## <a name="read-json-files"></a>Читать файлы JSON

Для обработки файлов JSON с использованием JSON_VALUE и [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), необходимо прочитать файл JSON из хранилища в виде одного столбца. Следующий скрипт читает файл *book1.json* в виде одной колонки:

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
> Вы читаете весь файл JSON как одну строку или столбец. Так, ФИЛЕТЕРМИНАТОР, ФИЛЕЛСЦИТЕиТЕРИТТЕР и ROWTERMINATOR настроены на 0x0b.

## <a name="query-json-files-using-json_value"></a>Запрос ы JSON файлы с использованием JSON_VALUE

Запрос ниже показывает, как использовать [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (название, издатель) из книги, озаглавленной *Вероятностные и статистические методы в криптологии, Введение выбранных статей*:

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

## <a name="query-json-files-using-json_query"></a>Запрос ы JSON файлы с использованием JSON_QUERY

Следующий запрос показывает, как использовать [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для извлечения объектов и массивов (авторов) из книги, озаглавленной *Вероятностные и статистические методы в криптологии, Введение выбранных тем:*

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

## <a name="query-json-files-using-openjson"></a>Запрос ы JSON файлы с помощью OPENJSON

В следующем запросе используется [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Он будет получать объекты и свойства в книге, озаглавленной *Вероятностные и статистические методы в криптологии, Введение по выбранным статьям*:

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
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Следующие шаги

Следующие статьи в этой серии продемонстрируют, как:

- [Папки запросов и несколько файлов](query-folders-multiple-csv-files.md)
- [Создание и использование представлений](create-use-views.md)
