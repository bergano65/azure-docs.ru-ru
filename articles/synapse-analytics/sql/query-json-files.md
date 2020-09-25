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
ms.reviewer: jrasnick
ms.openlocfilehash: 0757c867d46144ac9fb9b9eca8b2a588aeeb15d6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288330"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Запрос вложенных типов Parquet с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics

В этой статье вы узнаете, как написать запрос с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics. Цель запроса — чтение JSON-файлов с помощью [OPENROWSET](develop-openrowset.md). 
- Стандартные JSON-файлы, в которых хранятся несколько документов JSON в виде массива JSON.
- Разделенные строками JSON-файлы, в которых документы JSON разделяются символом новой строки. К общим расширениям для этих типов файлов относятся `jsonl` , `ldjson` и `ndjson` .

## <a name="read-json-documents"></a>Чтение документов JSON

Самый простой способ увидеть содержимое файла JSON — предоставить ему URL-адрес файла `OPENROWSET` функции, указать CSV `FORMAT` и задать значения `0x0b` для параметров `fieldterminator` и `fieldquote` . Если необходимо считать файлы JSON, разделенные строками, это достаточно. Если у вас есть классический файл JSON, необходимо задать значения `0x0b` для параметра `rowterminator` . `OPENROWSET` функция будет анализировать JSON и возвращать каждый документ в следующем формате:

| doc |
| --- |
|{«date_rep»: «2020-07-24», «Day»: 24, «month»: 7, «Year»: 2020, «cases»: 3, «смерти»: 0, «geo_id»: «AF»}|
|{"date_rep": "2020-07-25", "Day": 25, "месяц": 7, "Year": 2020, "cases": 7, "смерти": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "Day": 26, "месяц": 7, "Year": 2020, "cases": 4, "смерти": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "Day": 27, "месяц": 7, "Year": 2020, "cases": 8, "смерти": 0, "geo_id": "AF"}|

Если файл является общедоступным или удостоверение Azure AD может получить доступ к этому файлу, вы увидите содержимое файла с помощью запроса, как показано в следующих примерах.

### <a name="read-json-files"></a>Чтение файлов JSON

Следующий пример запроса считывает JSON-файлы, разделенные строками, и возвращает каждый документ в виде отдельной строки.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Этот запрос возвратит каждый документ JSON в виде отдельной строки результирующего набора. Убедитесь, что у вас есть доступ к этому файлу. Если файл защищен с помощью ключа SAS или пользовательского удостоверения, необходимо настроить [учетные данные на уровне сервера для входа SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Использование источника данных

В предыдущем примере используется полный путь к файлу. В качестве альтернативы можно создать внешний источник данных с расположением, которое указывает на корневую папку хранилища, и использовать этот источник данных и относительный путь к файлу в `OPENROWSET` функции:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Если источник данных защищен с помощью ключа SAS или пользовательского удостоверения, можно настроить [источник данных с учетными данными для базы данных](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

В следующих разделах можно увидеть, как запрашивать различные типы файлов JSON.

## <a name="parse-json-documents"></a>Анализ документов JSON

Запросы в предыдущих примерах возвращают каждый документ JSON в виде одной строки в отдельной строке результирующего набора. Можно использовать функции `JSON_VALUE` и `OPENJSON` для анализа значений в документах JSON и возврата их в виде реляционных значений, как показано в следующем примере:

| Представитель по дате \_ | cases | \_идентификатор Geo |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Пример документа JSON

В примерах запроса считываются файлы *JSON* , содержащие документы со следующей структурой:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Если эти документы хранятся в виде JSON, разделенного строками, необходимо задать `FIELDTERMINATOR` и значение `FIELDQUOTE` 0x0B. При наличии стандартного формата JSON необходимо задать значение `ROWTERMINATOR` 0x0B.

### <a name="query-json-files-using-json_value"></a>Запрашивайте файлы JSON с помощью JSON_VALUE

В следующем запросе показано, как использовать [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (Title, Publisher) из документов JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Запрашивание файлов JSON с помощью OPENJSON

В следующем запросе используется [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Будет получена статистика КОВИД, сообщаемая в Сербия:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Дальнейшие действия

В следующих статьях этой серии показано:

- [Запрашивание папок и нескольких файлов](query-folders-multiple-csv-files.md)
- [Создание и использование представлений](create-use-views.md)
