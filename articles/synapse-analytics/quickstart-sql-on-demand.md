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
ms.openlocfilehash: 43f361fbaf4ab0462af0a720d7711f219134a165
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692169"
---
# <a name="quickstart-using-sql-on-demand"></a>Краткое руководство. Использование SQL по запросу

Synapse SQL по запросу (предварительная версия) — это бессерверная служба запросов, которая позволяет выполнять SQL-запросы к файлам, размещенным в службе хранилища Azure. Из этого краткого руководства вы узнаете, как легко запрашивать файлы различных типов с помощью SQL по запросу.

Поддерживаются следующие типы файлов: JSON, CSV, Apache Parquet.

## <a name="prerequisites"></a>Предварительные требования

Выберите клиент SQL для создания запросов:

- [Azure Synapse Studio](quickstart-synapse-studio.md) — это веб-средство, которое можно использовать для просмотра файлов в хранилище и создания SQL-запросов.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) — это клиентское средство, которое позволяет выполнять SQL-запросы и записные книжки в базе данных по запросу.
- [SQL Server Management Studio](sql/get-started-ssms.md) — это клиентское средство, которое позволяет выполнять SQL-запросы к базе данных по запросу.

Параметры для краткого руководства:

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

### <a name="create-credentials"></a>Создание учетных данных

Чтобы выполнять запросы с помощью SQL по запросу, создайте учетные данные для SQL по запросу, которые будут использоваться для доступа к файлам в хранилище.

> [!NOTE]
> Для успешного выполнения примеров, описанных в этом разделе, необходимо использовать маркер SAS.
>
> Чтобы приступить к использованию маркеров SAS, необходимо удалить UserIdentity, который описан в этой [статье](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> По умолчанию для SQL по запросу всегда используется сквозной доступ через Azure Active Directory.

См. дополнительные сведения об [управлении доступом к учетной записи хранения для SQL по запросу ](sql/develop-storage-files-storage-access-control.md).

Выполните следующий фрагмент кода, чтобы создать учетные данные, используемые в примерах в этом разделе:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>Запрашивание CSV-файла

Следующее изображение приведено для предварительного просмотра файла для запроса:

![Первые 10 строк CSV-файла без заголовка, новая строка в стиле Windows](./sql/media/query-single-csv-file/population.png)

Следующий запрос показывает, как считать CSV-файл без строки заголовка, с новой строкой в стиле Windows и столбцами с разделителями-запятыми:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
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

## <a name="querying-parquet-files"></a>Запрашивание файлов Parquet

В следующем примере показаны возможности автоматического вывода схемы для создания запросов на файлы Parquet. Вызов возвращает число строк за сентябрь 2017 г. без указания схемы.

> [!NOTE]
> При чтении файлов Parquet указывать столбцы в предложении `OPENROWSET WITH` не требуется. В этом случае SQL по запросу использует метаданные в файле Parquet и привязывает столбцы по имени.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Дополнительные сведения см. в статье о [запросах файлов Parquet](sql/query-parquet-files.md).

## <a name="querying-json-files"></a>Запрашивание JSON-файлов

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

### <a name="querying-json-files"></a>Запрашивание JSON-файлов

В следующем запросе показано, как использовать параметр [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для получения скалярных значений (заголовка, издателя) из книги с заголовком *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* (Вероятностные и статистические методы в криптологии. Введение по избранным статьям):

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
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
