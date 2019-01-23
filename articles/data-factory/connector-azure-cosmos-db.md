---
title: Копирование данных в службу Azure Cosmos DB (API SQL) или из нее с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в службу Azure Cosmos DB (SQL API) или из нее в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 6dd7707c489bbbad7a97a0ec0a76e7c631bd1465
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359261"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure Cosmos DB (API SQL) или из нее с помощью Фабрики данных Azure

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1](v1/data-factory-azure-documentdb-connector.md)
> * [Текущая версия](connector-azure-cosmos-db.md)

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные в базу данных Azure Cosmos DB и из нее (API SQL). Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!NOTE]
>Этот соединитель поддерживает только копирование данных из API SQL Cosmos DB и из него. Сведения о MongoDB см. в статье [Копирование данных в базу данных Azure Cosmos DB (API MongoDB) или из нее с помощью Фабрики данных Azure](connector-azure-cosmos-db-mongodb-api.md). Другие типы API сейчас не поддерживаются.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из Azure Cosmos DB (SQL API) в любое поддерживаемое хранилище данных — приемник или скопировать данные из любого исходного хранилища данных в Azure Cosmos DB (SQL API). Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Возможности соединителя Azure Cosmos DB (SQL API)

- Копирование данных из службы Azure Cosmos DB и в нее с помощью [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Запись данных в Azure Cosmos DB с помощью операции **INSERT** или **UPSERT**.
- Импорт и экспорт документов JSON "как есть" либо копирование данных из набора табличных данных или в него. К примерам можно отнести базу данных SQL и CSV-файл. Сведения о копировании документов "как есть" в JSON-файлы или другую коллекцию Azure Cosmos DB либо из них см. в разделе [Импорт и экспорт документов JSON](#importexport-json-documents).

Фабрика данных интегрируется с [библиотекой массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started), чтобы обеспечить оптимальную производительность при операциях записи в Azure Cosmos DB.

> [!TIP]
> [Видео о переносе данных](https://youtu.be/5-SRNiC_qOU) поможет вам выполнить копирование данных из хранилища BLOB-объектов Azure в Azure Cosmos DB. Кроме того, в видео приведены общие рекомендации по настройке производительности приема данных в Azure Cosmos DB.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые можно использовать для определения сущностей фабрики данных, характерных для Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Cosmos DB (SQL API) поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства **type** необходимо задать значение: **CosmosDb**. | Yes |
| connectionString |Укажите сведения, необходимые для подключения к базе данных Azure Cosmos DB.<br /><br />**Примечание**. Необходимо указать сведения о базе данных в строке подключения, как показано в приведенных ниже примерах. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных. Вы можете также [указать секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать Azure Integration Runtime или локальную среду IR (если хранилище данных расположено в частной сети). Если это свойство не задано, используется Azure Integration Runtime по умолчанию. |Нет  |

**Пример**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Этот раздел содержит список свойств, поддерживаемых набором данных Azure Cosmos DB (SQL API). 

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md). 

Чтобы скопировать данные из службы Azure Cosmos DB (SQL API) или в нее, задайте для свойства **type** набора данных значение **DocumentDbCollection**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** должно иметь значение **DocumentDbCollection**. |Yes |
| collectionName |Имя коллекции документов Azure Cosmos DB. |Yes |

**Пример**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Схема фабрики данных

Для хранилищ данных без схемы, таких как Azure Cosmos DB, действие копирования определяет схему одним из приведенных ниже способов. Если только вам не требуется [импортировать или экспортировать документы JSON "как есть"](#import-or-export-json-documents), мы рекомендуем указывать структуру данных в разделе **structure**.

* Если указать структуру данных с помощью свойства **structure** в определении набора данных, фабрика данных считает эту структуру схемой. 

    Если строка не содержит значение столбца, ему присваивается значение NULL.
* Если структура данных не указана с помощью свойства **structure** в определении набора данных, то служба фабрики данных определяет схему, используя первую строку данных. 

    Если первая строка не содержит полную схему, после операции копирования некоторые столбцы будут отсутствовать.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Cosmos DB (SQL API).

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) как источник

Чтобы скопировать данные из Azure Cosmos DB (SQL API), в действии копирования в качестве типа **source** задайте значение **DocumentDbCollectionSource**. 

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** источника действия копирования должно иметь значение **DocumentDbCollectionSource**. |Yes |
| query |Укажите запрос Azure Cosmos DB для чтения данных.<br/><br/>Пример:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Нет  <br/><br/>Если не указано, то выполняется инструкция SQL `select <columns defined in structure> from mycollection`. |
| nestingSeparator |Специальный знак, обозначающий, что документ является вложенным, и определяющий, как преобразовать результирующий набор в плоскую структуру.<br/><br/>Например, если запрос Azure Cosmos DB возвращает вложенный результат `"Name": {"First": "John"}`, действие копирования идентифицирует имя колонки как `Name.First` со значением "John", в котором **nestedSeparator** является точкой (**.** ) |Нет <br />Значение по умолчанию — **.** (точка). |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) как приемник

Чтобы скопировать данные в Azure Cosmos DB (SQL API), в действии копирования задайте для типа **sink** значение **DocumentDbCollectionSink**. 

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство **type** приемника действия копирования должно иметь значение **DocumentDbCollectionSink**. |Yes |
| writeBehavior |Описывает способ записи данных в Azure Cosmos DB. Допустимые значения: **insert** и **upsert**.<br/><br/>Поведение **upsert** — замена документа, если документ с таким идентификатором уже существует. В противном выполняется вставка документа.<br /><br />**Примечание**. Фабрика данных автоматически создает идентификатор для документа, если идентификатор не указан в исходном документе или с помощью сопоставления столбцов. Это означает, что для правильной работы **upsert** у документа должен быть идентификатор. |Нет <br />(По умолчанию используется **insert**.) |
| writeBatchSize | Фабрика данных использует [библиотеку массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) для записи данных в Azure Cosmos DB. Свойство **writeBatchSize** определяет размер документов, которые ADF передает в библиотеку. Вы можете увеличить значение **writeBatchSize** для повышения производительности или уменьшить значение, если документ большого размера. См. рекомендации ниже. |Нет <br />(Значение по умолчанию — **10 000**.) |
| nestingSeparator |Специальный знак в имени **исходного** столбца, который указывает, что нужен вложенный документ. <br/><br/>Например, `Name.First` в выходной структуре набора данных создает приведенную ниже структуру JSON в документе Azure Cosmos DB, в которой **nestedSeparator** является точкой (**.** ) `"Name": {"First": "[value maps to this column from source]"}`  |Нет <br />Значение по умолчанию — **.** (точка). |

>[!TIP]
>В Cosmos DB размер одного запроса не должен превышать 2 МБ. Формула выглядит так: размер запроса = размера одного документа * размер пакета для записи. Если появится ошибка **Запрос слишком большой**, **уменьшите значение `writeBatchSize`** в конфигурации приемника действия копирования.

**Пример**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>Импорт или экспорт документов JSON

Соединитель Azure Cosmos DB (SQL API) помогает легко:

* импортировать документы JSON из различных источников в Azure Cosmos DB, включая хранилище BLOB-объектов Azure, Azure Data Lake Store или другие файловые хранилища, поддерживаемые Фабрикой данных Azure;
* экспортировать документы JSON из коллекции Azure Cosmos DB в различные файловые хранилища;
* копировать документы между двумя коллекциями Azure Cosmos DB "как есть".

Чтоб обеспечить копирование без использования схем, сделайте следующее.

* При использовании инструмента "Копирование данных" установите флажок **Export as-is to JSON files or DocumentDB collection** (Экспортировать "как есть" в JSON-файлы или коллекцию Cosmos DB).
* При разработке действий не следует указывать раздел **structure** (также называемый *схемой*) в наборе данных Azure Cosmos DB. Кроме того, не следует указывать свойство **nestingSeparator** в источнике или приемнике Azure Cosmos DB в действии копирования. При импорте из JSON-файлов или экспорте в них в соответствующем наборе данных файлового хранилища укажите для типа **format** значение **JsonFormat** и настройте **filePattern**, как описано в разделе [Формат JSON](supported-file-formats-and-compression-codecs.md#json-format). Не указывайте раздел **structure** и пропустите остальные параметры формата.

## <a name="next-steps"></a>Дополнительная информация

В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
