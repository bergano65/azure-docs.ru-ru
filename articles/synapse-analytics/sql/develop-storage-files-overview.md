---
title: Получение доступа к файлам в хранилище с помощью SQL по запросу (предварительная версия) в Synapse SQL
description: Узнайте, как запрашивать файлы хранилища с помощью ресурсов SQL по запросу (предварительная версия) в Synapse SQL
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c251b70d1988be82821f1e133151dae1ac6d1bc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921294"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>Получение доступа к внешнему хранилищу в Synapse SQL (по запросу)

В этом документе описывается, как пользователь может считывать данные из файлов, хранящихся в службе хранилища Azure, в Synapse SQL (по запросу). Пользователи могут обращаться к хранилищу с помощью следующих средств:

- [OPENROWSET](develop-openrowset.md) — функция, которая позволяет выполнять специальные запросы к файлам в службе хранилища Azure.
- [Внешняя таблица](develop-tables-external-tables.md) — предопределенная структура данных, созданная на основе набора внешних файлов.

Пользователь может использовать [разные методы аутентификации](develop-storage-files-storage-access-control.md), такие как сквозная аутентификация Azure AD (по умолчанию для участников Azure AD) и аутентификация SAS (по умолчанию для участников SQL).

## <a name="openrowset"></a>OPENROWSET

С помощью функции [OPENROWSET](develop-openrowset.md) пользователь может считывать файлы из службы хранилища Azure.

### <a name="query-files-using-openrowset"></a>Запрашивание файлов с помощью OPENROWSET

С помощью функции OPENROWSET пользователи могут запрашивать внешние файлы в службе хранилища Azure, если у них есть доступ к хранилищу. Пользователь, подключенный к конечной точке Synapse SQL по запросу, должен использовать следующий запрос для чтения содержимого файлов в службе хранилища Azure:

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

Пользователь может получить доступ к хранилищу на основе следующих правил доступа:

- Пользователь Azure AD — OPENROWSET будет использовать удостоверение Azure AD вызывающей стороны для обращения к службе хранилища Azure или хранилищу с анонимным доступом.
- Пользователь SQL — OPENROWSET будет обращаться к хранилищу с анонимным доступом.

Субъекты SQL также могут использовать OPENROWSET для непосредственного запрашивания файлов, защищенных маркерами SAS или управляемым удостоверением рабочей области. Если пользователь SQL выполняет эту функцию, опытный пользователь с разрешением ALTER ANY CREDENTIAL должен создать учетные данные уровня сервера, соответствующие URL-адресу в функции (с использованием имени хранилища и контейнера) и предоставить разрешения REFERENCES для этих учетных данных вызывающему объекту функции OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

Если учетные данные уровня сервера, которые соответствуют URL-адресу, отсутствуют или у пользователя SQL нет разрешения REFERENCES для этих учетных данных, будет возвращена ошибка. Субъекты SQL не поддерживают олицетворение с помощью некоторых удостоверений Azure AD.

> [!NOTE]
> Эта версия OPENROWSET предназначена для быстрого и удобного просмотра данных с использованием аутентификации по умолчанию. Чтобы работать с олицетворением или управляемым удостоверением, используйте OPENROWSET с DATASOURCE, как описано в следующем разделе.

### <a name="querying-data-sources-using-openrowset"></a>Обращение к источникам данных с помощью OPENROWSET

С помощью функции OPENROWSET пользователь может запрашивать файлы, размещенные во внешнем источнике данных:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

Опытным пользователям с разрешением CONTROL DATABASE нужно создать учетные данные уровня базы данных (DATABASE SCOPED CREDENTIAL) для обращения к хранилищу и внешнему источнику данных (EXTERNAL DATA SOURCE) с определением используемых URL-адреса источника данных и учетных данных:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

Учетные данные уровня базы данных определяют способ доступа к файлам в указанном источнике данных (сейчас это SAS и управляемое удостоверение).

Вызывающий объект должен иметь одно из следующих разрешений для выполнения функции OPENROWSET:

- Одно из разрешений на выполнение функции OPENROWSET:
  - ADMINISTER BULK OPERATION позволяет выполнить вход для выполнения функции OPENROWSET.
  - ADMINISTER DATABASE BULK OPERATION позволяет пользователю базы данных выполнить функцию OPENROWSET.
- REFERENCES DATABASE SCOPED CREDENTIAL для учетных данных, указанных в EXTERNAL DATA SOURCE.

#### <a name="accessing-anonymous-data-sources"></a>Получение доступа к анонимным источникам данных

Пользователь может создать внешний источник данных без учетных данных со ссылкой на хранилище с открытым доступом ИЛИ использовать сквозную аутентификацию Azure AD.

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>EXTERNAL TABLE

Пользователь с разрешениями на чтение таблицы может обращаться к внешним файлам с помощью внешней таблицы (EXTERNAL TABLE), созданной на основе набора папок и файлов службы хранилища Azure.

Пользователь с [разрешениями на создание внешней таблицы](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) (например, CREATE TABLE и ALTER ANY CREDENTIAL или REFERENCES DATABASE SCOPED CREDENTIAL), может использовать следующий скрипт для создания таблицы на основе источника данных службы хранилища Azure:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

Пользователям с разрешением CONTROL DATABASE нужно создать учетные данные уровня базы данных (DATABASE SCOPED CREDENTIAL) для обращения к хранилищу и внешнему источнику данных (EXTERNAL DATA SOURCE) с определением используемых URL-адреса источника данных и учетных данных:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

Учетные данные уровня базы данных определяют способ доступа к файлам в указанном источнике данных.

### <a name="reading-external-files-with-external-table"></a>Считывание внешних файлов с помощью EXTERNAL TABLE

Структура EXTERNAL TABLE позволяет считывать данные из файлов, на которые указывает источник данных, с помощью стандартной инструкции SQL SELECT:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Для чтения данных вызывающий объект должен иметь следующие разрешения:
- Разрешение `SELECT` ДЛЯ внешней таблицы.
- Разрешение `REFERENCES DATABASE SCOPED CREDENTIAL`, если у `DATA SOURCE` есть `CREDENTIAL`.

## <a name="permissions"></a>Разрешения

В следующей таблице перечислены необходимые разрешения для перечисленных выше операций.

| Запрос | Необходимые разрешения|
| --- | --- |
| OPENROWSET(BULK) без источника данных | `ADMINISTER BULK ADMIN`, `ADMINISTER DATABASE BULK ADMIN` или имя для входа SQL должны содержать REFERENCES CREDENTIAL::\<URL> для хранилища, защищенного с помощью SAS |
| OPENROWSET(BULK) с источником данных без учетных данных | `ADMINISTER BULK ADMIN` или `ADMINISTER DATABASE BULK ADMIN` |
| OPENROWSET(BULK) с источником данных с учетными данными | `ADMINISTER BULK ADMIN`, `ADMINISTER DATABASE BULK ADMIN` или `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` и `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` и `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` и `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Для создания таблиц: `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` и `ALTER ANY EXTERNAL FILE FORMAT`; для чтения данных: `ADMIN BULK OPERATIONS`, `REFERENCES CREDENTIAL` или `SELECT TABLE` для каждых таблицы, представления или функции в запросе, а также разрешение на чтение и запись в хранилище. |

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы для работы со следующими руководствами:

- [Запрашивание данных в хранилище](query-data-storage.md)

- [Запрашивание CSV-файлов](query-single-csv-file.md)

- [Запрашивание папок и нескольких файлов](query-folders-multiple-csv-files.md)

- [Запрашивание конкретных файлов](query-specific-files.md)

- [Запрашивание файлов Parquet](query-parquet-files.md)

- [Запрашивание вложенных типов](query-parquet-nested-types.md)

- [Запрашивание JSON-файлов](query-json-files.md)

- [Создание и использование представлений](create-use-views.md)
