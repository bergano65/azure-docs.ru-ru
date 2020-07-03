---
title: Общие сведения о запрашивании данных в хранилище с помощью SQL по запросу (предварительная версия)
description: В этом разделе содержатся примеры запросов, которые можно использовать для работы с ресурсом SQL по запросу (предварительной версии) в Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116253"
---
# <a name="overview-query-data-in-storage"></a>Общие сведения. Запрашивание данных в хранилище

В этом разделе содержатся примеры запросов, которые можно использовать для работы с ресурсом SQL по запросу (предварительной версии) в Azure Synapse Analytics.
В настоящее время поддерживаются следующие файлы: 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны средства, необходимые для создания запросов.

- Выберите клиент SQL:
    - Azure Synapse Studio (предварительная версия)
    - Azure Data Studio
    - SQL Server Management Studio

Кроме того, используются следующие параметры:

| Параметр                                 | Описание                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Адрес конечной точки службы SQL по запросу    | Будет использоваться в качестве имени сервера.                                   |
| Регион конечной точки службы SQL по запросу     | Будет использоваться для определения хранилища, используемого в примерах. |
| Имя пользователя и пароль для доступа к конечной точке | Будет использоваться для доступа к конечной точке.                               |
| База данных, которая будет использоваться для создания представлений     | Эта база данных будет использоваться в качестве начальной точки в примерах.       |

## <a name="first-time-setup"></a>Изначальная настройка

При использовании приведенных далее примеров необходимо выполнить два действия:

- создайте базу данных для представлений (если необходимо использовать представления);
- создайте учетные данные, которые будут использоваться SQL по запросу для доступа к файлам в хранилище.

### <a name="create-database"></a>Создание базы данных

Для создания представлений понадобится база данных. Используйте эту базу данных в примерах запросов в этой документации.

> [!NOTE]
> Базы данных используются только для просмотра метаданных, а не для реальных данных.  Запишите имя используемой базы данных, ведь оно потребуется позже.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Создание учетных данных

Перед выполнением запросов следует создать учетные данные. Эти учетные данные будут использоваться службой SQL по запросу для доступа к файлам в хранилище.

> [!NOTE]
> Для успешного выполнения указаний в этом разделе необходимо использовать маркер SAS.
>
> Чтобы приступить к использованию маркеров SAS, необходимо удалить UserIdentity, который описан в этой [статье](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> По умолчанию для SQL по запросу всегда используется сквозной доступ через Azure Active Directory.

Дополнительные сведения об управлении доступом к хранилищу см. по этой [ссылке](develop-storage-files-storage-access-control.md).

Чтобы создать учетные данные для контейнеров CSV, JSON и Parquet, выполните следующий код:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Предоставление демонстрационных данных

Демонстрационные данные содержат указанные ниже наборы данных.

- NYC Taxi - Yellow Taxi Trip Records (Такси Нью-Йорка. Записи поездок такси желтого цвета) — часть общедоступного набора данных по Нью-Йорку:
  - Формат CSV
  - Формат Parquet
- Набор данных о населении:
  - Формат CSV
- Примеры файлов Parquet со вложенными столбцами:
  - Формат Parquet
- Книги JSON:
  - Формат JSON

| Путь к папке                                                  | Описание                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Родительская папка для данных в формате CSV.                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Папки с файлами данных о населении в разных форматах CSV. |
| /csv/taxi/                                                   | Папка с общедоступными файлами данных по Нью-Йорку в формате CSV.              |
| /parquet/                                                    | Родительская папка для данных в формате Parquet.                     |
| /parquet/taxi                                                | Общедоступные файлы данных по Нью-Йорку в формате Parquet, секционированные по годам и месяцам с использованием схемы секционирования Hive на платформе Hadoop. |
| /parquet/nested/                                             | Примеры файлов Parquet со вложенными столбцами:                     |
| /json/                                                       | Родительская папка для данных в формате JSON.                        |
| /json/books/                                                 | Файлы JSON с данными книг.                                   |

## <a name="validation"></a>Проверка

Выполните следующие три запроса и проверьте, правильно ли созданы учетные данные.

> [!NOTE]
> Все универсальные коды ресурсов (URI) в примерах запросов используют учетную запись хранения, расположенную в регионе Azure "Северная Европа". Убедитесь, что вы создали соответствующие учетные данные. Выполните приведенный ниже запрос и убедитесь, что в списке указана учетная запись хранения.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Если вы не можете найти соответствующие учетные данные, см. раздел [Изначальная установка](#first-time-setup).

### <a name="sample-query"></a>Пример запроса

Последний шаг проверки — выполнение следующего запроса:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

Этот запрос выше должен вернуть такое число: **8945574**.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы для работы со следующими руководствами:

- [Запрашивание одного CSV-файла](query-single-csv-file.md)

- [Запрашивание папок и нескольких CSV-файлов](query-folders-multiple-csv-files.md)

- [Запрашивание конкретных файлов](query-specific-files.md)

- [Запрашивание файлов Parquet](query-parquet-files.md)

- [Запрашивание вложенных типов Parquet](query-parquet-nested-types.md)

- [Запрашивание JSON-файлов](query-json-files.md)

- [Создание и использование представлений](create-use-views.md)
