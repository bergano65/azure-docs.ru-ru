---
title: Запрос Azure Cosmos DB данных с помощью SQL Server не в связи с Azure синапсе (Предварительная версия)
description: В этой статье вы узнаете, как запросить Azure Cosmos DB с помощью SQL по запросу в Azure синапсе Link (Предварительная версия).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: c326aed172bb8159185829f80d66e8e00496aad2
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057813"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Запрос Azure Cosmos DB данных с помощью SQL Server не в связи с Azure синапсе (Предварительная версия)

Синапсе SQL Server (ранее по запросу) позволяет анализировать данные в контейнерах Azure Cosmos DB, которые включены с помощью [ссылки Azure синапсе](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) практически в реальном времени, не влияя на производительность транзакционных рабочих нагрузок. Он предлагает знакомый синтаксис T-SQL для запроса данных из [аналитического хранилища](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) и интегрированного подключения к широкому спектру средств BI и специальных запросов через интерфейс T-SQL.

> [!NOTE]
> Поддержка запросов Azure Cosmos DB аналитического хранилища с SQL Server в настоящее время находится в режиме предварительной версии. Общедоступная Предварительная версия будет объявлена на странице [обновлений службы Azure](https://azure.microsoft.com/updates/?status=nowavailable&category=databases) .

Для запросов Azure Cosmos DB Полная контактная зона [выбора](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) поддерживается с помощью функции [OPENROWSET](develop-openrowset.md) , включая большинство [функций и операторов SQL](overview-features.md). Кроме того, можно сохранять результаты запроса, считывающего данные из Azure Cosmos DB вместе с данными в хранилище BLOB-объектов Azure, или Azure Data Lake Storage использовать [команду создать внешнюю таблицу как SELECT](develop-tables-cetas.md#cetas-in-sql-on-demand). В настоящее время нельзя хранить результаты запросов SQL Server, чтобы Azure Cosmos DB с помощью [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

В этой статье вы узнаете, как написать запрос с помощью SQL Server, который будет запрашивать данные из Azure Cosmos DB контейнеров, Синапсеных с включенной ссылкой. Затем вы можете узнать больше о создании бессерверных представлений SQL Server в Azure Cosmos DB контейнерах и их подключении к Power BI моделям в [этом](./tutorial-data-analyst.md) руководстве. 

## <a name="overview"></a>Обзор

Для поддержки запросов и анализа данных в Azure Cosmos DB аналитическом хранилище SQL Server не использует следующий `OPENROWSET` синтаксис:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Строка подключения Azure Cosmos DB указывает имя учетной записи Azure Cosmos DB, имя базы данных, главный ключ учетной записи базы данных и необязательное имя области для `OPENROWSET` работы. 

> [!IMPORTANT]
> Убедитесь, что используется псевдоним после `OPENROWSET` . Существует [известная ошибка](#known-issues) , которая вызывает проблемы с подключением к синапсе КОНЕЧНОЙ точке SQL без сервера, если не указать псевдоним после `OPENROWSET` функции.

Строка подключения имеет следующий формат:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Имя контейнера Azure Cosmos DB указывается без кавычек в `OPENROWSET` синтаксисе. Если имя контейнера содержит специальные символы (например, дефис "-"), имя должно быть заключено в `[]` квадратные скобки в `OPENROWSET` синтаксисе.

> [!NOTE]
> SQL Server без поддержки запросов Azure Cosmos DB хранилище транзакций.

## <a name="sample-data-set"></a>Пример набора данных

Примеры в этой статье основаны на данных из [Европейского центра для предотвращения болезни и контроля (ЕКДК) ковид-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) и [ковид-19 Open Research DataSet (шнур-19), ДОИ: 10.5281/зенодо. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Вы можете просмотреть лицензию и структуру данных на этих страницах, а также загрузить демонстрационные данные для наборов данных [ЕКДК](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) и [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Для работы с этой статьей, посвященной запросам Cosmos DB данных с помощью SQL Server, убедитесь, что созданы следующие ресурсы:
* Azure Cosmos DBная учетная запись базы данных [синапсе включена](../../cosmos-db/configure-synapse-link.md)
* База данных Azure Cosmos DB с именем `covid`
* Два контейнера Azure Cosmos DB с именами `EcdcCases` и `Cord19` с приведенными выше наборами данных выборки загружены.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Просмотр Azure Cosmos DB данных с помощью автоматического вывода схемы

Самый простой способ исследовать данные в Azure Cosmos DB заключается в использовании функции автоматического вывода схемы. Пропуская `WITH` предложение из `OPENROWSET` инструкции, можно указать, что SQL Server не должен автоматически обнаруживать (выводить) схему аналитического хранилища контейнера Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
В приведенном выше примере мы предписываете SQL Server не подключаться к `covid` базе данных в учетной записи Azure Cosmos DB `MyCosmosDbAccount` , прошедшей проверку подлинности с помощью Azure Cosmos DB ключа (в примере выше). Затем осуществляется доступ к `EcdcCases` аналитическему хранилищу контейнера в `West US 2` регионе. Поскольку нет проекции конкретных свойств, функция возвратит `OPENROWSET` все свойства из Azure Cosmos DB элементов.

Если необходимо исследовать данные из другого контейнера в той же Azure Cosmos DB базе данных, можно использовать одну и ту же строку подключения и ссылку на необходимый контейнер в качестве третьего параметра:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Явное указание схемы

Хотя функция автоматического вывода схемы в `OPENROWSET` предоставляет простой и простой в использовании куериенце, для бизнес-сценариев может потребоваться явно указать схему для получения релевантных свойств, предназначенных только для чтения, из Azure Cosmos DB данных.

`OPENROWSET` позволяет явно указать свойства, которые требуется считать из данных в контейнере, и указать их типы данных. Предположим, что мы импортировали некоторые данные из [набора данных ЕКДК ковид](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) со следующей структурой в Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Эти плоские документы JSON в Azure Cosmos DB могут быть представлены в виде набора строк и столбцов в синапсе SQL. `OPENROWSET` позволяет указать подмножество свойств, которые необходимо считать, и точные типы столбцов в `WITH` предложении:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Результат этого запроса может выглядеть следующим образом:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | Сервер отчетов |
| 2020-08-12 | 235 | Сервер отчетов |
| 2020-08-11 | 163 | Сервер отчетов |

Просмотрите [правила сопоставления типов SQL](#azure-cosmos-db-to-sql-type-mappings) в конце статьи, чтобы получить дополнительные сведения о типах SQL, которые следует использовать для Azure Cosmos DBного значения.

## <a name="querying-nested-objects-and-arrays"></a>Запрос вложенных объектов и массивов

Azure Cosmos DB позволяет представлять более сложные модели данных, составляя их как вложенные объекты или массивы. Функция автосинхронизации ссылки синапсе для Azure Cosmos DB управляет представлением схемы в расширенном хранилище, которое включает в себя обработку вложенных типов данных, позволяющих выполнять расширенные запросы из SQL Server.

Например, в наборе данных " [шнур-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) " есть документы JSON, которые приведены в следующей структуре:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Вложенные объекты и массивы в Azure Cosmos DB представлены в результатах запроса в виде строк JSON, когда `OPENROWSET` функция считывает их. Один из вариантов чтения значений из этих сложных типов в качестве столбцов SQL использует функции SQL JSON.

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Результат этого запроса может выглядеть следующим образом:

| title | authors | first_autor_name |
| --- | --- | --- |
| Дополнительная информация — епидеми... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Жюльен |  

В качестве альтернативного варианта можно также указать пути к вложенным значениям в объектах при использовании `WITH` предложения:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Дополнительные сведения о анализе [сложных типов данных в ссылках синапсе](../how-to-analyze-complex-schema.md) и [вложенных структурах в SQL Server](query-parquet-nested-types.md).

> [!IMPORTANT]
> Если в тексте отображаются непредвиденные символы, например, `MÃƒÂ©lade` вместо этого `Mélade` Параметры сортировки базы данных не задаются в [кодировке UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Измените параметры сортировки базы данных](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) на некоторые параметры сортировки UTF8, используя инструкцию SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` , например.


## <a name="flattening-nested-arrays"></a>Сведение вложенных массивов

Azure Cosmos DB данные могут иметь вложенные вложенные массивы, такие как массивы авторов из набора данных [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

В некоторых случаях может потребоваться "присоединить" к свойствам верхнего элемента (метаданных) со всеми элементами массива (авторы). SQL Server не позволяет выполнять сведение вложенных структур путем применения `OPENJSON` функции к вложенному массиву:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Результат этого запроса может выглядеть следующим образом:

| title | authors | first | last | принадлежность |
| --- | --- | --- | --- | --- |
| Дополнительная информация — епидеми... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Жюльен | мéладе | `   {"laboratory":"Centre de Recher…` |
Дополнительная информация — епидеми... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | николас | четырех # |`{"laboratory":"","institution":"U…` | 
| Дополнительная информация — епидеми... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | беза | рамазиндразана | `{"laboratory":"Centre de Recher…` |
| Дополнительная информация — епидеми... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | оливиер | флорес |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Если в тексте отображаются непредвиденные символы, например, `MÃƒÂ©lade` вместо этого `Mélade` Параметры сортировки базы данных не задаются в [кодировке UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Измените параметры сортировки базы данных](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) на некоторые параметры сортировки UTF8, используя инструкцию SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` , например.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Сопоставления типов SQL Azure Cosmos DB

Важно отметить, что хотя Azure Cosmos DB хранилище транзакций не зависит от схемы, аналитическое хранилище схематизированных для оптимизации производительности аналитических запросов. С помощью функции автосинхронизации канала синапсе Azure Cosmos DB управляет представлением схемы в средстве аналитического хранилища, которое включает обработку вложенных типов данных. Поскольку SQL Server не запрашивает аналитическое хранилище, важно понимать, как сопоставлять типы входных данных Azure Cosmos DB с типами данных SQL.

Учетные записи Azure Cosmos DB API SQL (Core) поддерживают типы свойств JSON чисел, строковых, логических, null, вложенных объектов или массивов. При использовании предложения в среде необходимо выбрать типы SQL, соответствующие этим типам JSON `WITH` `OPENROWSET` . См. ниже типы столбцов SQL, которые следует использовать для различных типов свойств в Azure Cosmos DB.

| Тип свойства Azure Cosmos DB | Тип столбца SQL |
| --- | --- |
| Логическое значение | bit |
| Целое число | BIGINT |
| Decimal | FLOAT |
| Строка | varchar (параметры сортировки базы данных UTF8) |
| Дата и время (строка в формате ISO) | varchar (30) |
| Дата и время (метка времени Unix) | BIGINT |
| NULL | `any SQL type` 
| Вложенный объект или массив | varchar (max) (параметры сортировки базы данных UTF8), сериализованный как текст JSON |

Для запроса Azure Cosmos DB учетных записей типа API Mongo DB вы можете узнать больше о полном представлении схемы точности в аналитическом хранилище и именах расширенных свойств, которые будут использоваться [здесь](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

## <a name="known-issues"></a>Известные проблемы

- Псевдоним **должен** быть указан после `OPENROWSET` функции (например, `OPENROWSET (...) AS function_alias` ). Пропуск псевдонима может вызвать проблемы с подключением и синапсе конечную точку SQL без сервера, которая может быть временно недоступна. Эта проблема будет устранена в 2020 ноября.
- Синапсе SQL без сервера в настоящее время не поддерживает [Azure Cosmos DB схему полной точности](../../cosmos-db/analytical-store-introduction.md#schema-representation). Синапсе SQL Server можно использовать только для доступа к четко определенной схеме Cosmos DB.

Вы можете сообщить о предложениях и проблемах на [странице отзывов о службе Azure синапсе](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях:

- [Инструкции по созданию и использованию представлений в SQL по запросу](create-use-views.md) 
- [Руководство по созданию представлений SQL по запросу Azure Cosmos DB и их подключению к Power BIным моделям через DirectQuery](./tutorial-data-analyst.md)
