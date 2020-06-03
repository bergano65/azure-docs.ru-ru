---
title: Использование SQL по запросу (предварительная версия)
description: Из этого краткого руководства вы узнаете, как легко запрашивать файлы различных типов с помощью SQL по запросу (предварительная версия).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 6d107dcbdc31a0049c7685e6dd8223bda694a526
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836810"
---
# <a name="quickstart-use-sql-on-demand"></a>Краткое руководство. Использование службы SQL по запросу

Synapse SQL по запросу (предварительная версия) — это бессерверная служба запросов, которая позволяет выполнять SQL-запросы к файлам, размещенным в службе хранилища Azure. Из этого краткого руководства вы узнаете, как легко запрашивать файлы различных типов с помощью SQL по запросу. Поддерживаемые форматы перечислены в [OPENROWSET](sql/develop-openrowset.md).

В этом кратком руководстве объясняется, как выполнять запросы к файлам CSV, Apache Parquet и JSON.

## <a name="prerequisites"></a>Предварительные требования

Выберите клиент SQL для создания запросов:

- [Azure Synapse Studio](quickstart-synapse-studio.md) — это веб-средство, которое можно использовать для просмотра файлов в хранилище и создания SQL-запросов.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) — это клиентское средство, которое позволяет выполнять SQL-запросы и записные книжки в базе данных по запросу.
- [SQL Server Management Studio](sql/get-started-ssms.md) — это клиентское средство, которое позволяет выполнять SQL-запросы к базе данных по запросу.

Параметры для этого краткого руководства:

| Параметр                                 | Описание                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Адрес конечной точки службы SQL по запросу    | Используется в качестве имени сервера.                                   |
| Регион конечной точки службы SQL по запросу     | Используется, чтобы определить хранилище для использования в примерах. |
| Имя пользователя и пароль для доступа к конечной точке | Используется для доступа к конечной точке.                               |
| База данных, используемая для создания представлений         | База данных, используемая в качестве начальной точки в примерах.       |

## <a name="first-time-setup"></a>Изначальная настройка

Прежде чем использовать примеры, сделайте следующее:

- создайте базу данных для представлений (если необходимо использовать представления);
- создайте учетные данные, которые будут использоваться SQL по запросу для доступа к файлам в хранилище.

### <a name="create-database"></a>Создание базы данных

Создайте собственную базу данных для демонстрационных целей. Эта база данных будет использоваться для создания представлений и для примеров запросов в этой статье.

> [!NOTE]
> Базы данных используются только для просмотра метаданных, а не для реальных данных.
>Запишите имя базы данных, которое будет использоваться далее для работы с этим кратким руководством.

Используйте следующий запрос, изменив данные в поле `mydbname` на необходимое имя:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Создание источника данных

Чтобы выполнять запросы с помощью SQL по запросу, создайте источник данных, который SQL по запросу сможет использовать для доступа к файлам в хранилище.
Выполните следующий фрагмент кода, чтобы создать источник данных, используемые в примерах в этом разделе:

```sql
-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Запросы к CSV-файлам

Следующее изображение приведено для предварительного просмотра файла для запроса:

![Первые 10 строк CSV-файла без заголовка, новая строка в стиле Windows](./sql/media/query-single-csv-file/population.png)

Следующий запрос показывает, как считать CSV-файл без строки заголовка, с новой строкой в стиле Windows и столбцами с разделителями-запятыми:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Схему можно указать во время компиляции запроса.
Дополнительные примеры см. в статье о [запросах CSV-файлов](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Запрашивание файлов Parquet

В следующем примере показаны возможности автоматического вывода схемы для создания запросов на файлы Parquet. Вызов возвращает число строк за сентябрь 2017 г. без указания схемы.

> [!NOTE]
> При чтении файлов Parquet указывать столбцы в предложении `OPENROWSET WITH` не требуется. В этом случае SQL по запросу использует метаданные в файле Parquet и привязывает столбцы по имени.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Дополнительные сведения см. в статье о [запросах файлов Parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Запрашивание файлов JSON

### <a name="json-sample-file"></a>Образец JSON-файла

Файлы хранятся в контейнере *JSON*, в папке *Books* и содержат по одной записи книги со следующей структурой:

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

### <a name="query-json-files"></a>Запрашивание файлов JSON

В следующем запросе показано, как использовать параметр [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (заголовка, издателя) из книги с заголовком *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* (Вероятностные и статистические методы в криптологии. Введение по избранным статьям):

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Выполняется считывание всего JSON-файла в виде одной строки или столбца. Поэтому для параметров IELDTERMINATOR, FIELDQUOTE и ROWTERMINATOR установлено значение 0x0b, так как мы не ожидаем найти их в файле.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы для работы со следующими статьями:

- [Запрашивание одного CSV-файла](sql/query-single-csv-file.md)
- [Запрашивание папок и нескольких CSV-файлов](sql/query-folders-multiple-csv-files.md)
- [Запрашивание конкретных файлов](sql/query-specific-files.md)
- [Запрашивание файлов Parquet](sql/query-parquet-files.md)
- [Запрашивание вложенных типов Parquet](sql/query-parquet-nested-types.md)
- [Запрашивание JSON-файлов](sql/query-json-files.md)
- [Создание и использование представлений](sql/create-use-views.md)
- [Создание и использование внешних таблиц](sql/create-use-external-tables.md)
- [Сохранение результата запроса в службе хранилища Azure](sql/create-external-table-as-select.md)
- [Запрашивание одного CSV-файла](sql/query-single-csv-file.md)
