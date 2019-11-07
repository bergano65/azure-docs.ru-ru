---
title: Копирование данных в API Azure Cosmos DB для MongoDB с помощью фабрики данных или из него
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в API службы Azure Cosmos DB для MongoDB или из него в поддерживаемые хранилища-приемники с помощью Фабрики данных.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: d0c90562c5e0810e5ed4898f6b2ec09ee52d16a0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681327"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Копирование данных в API службы Azure Cosmos DB для MongoDB или из него с помощью Фабрики данных Azure

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные в API службы Azure Cosmos DB для MongoDB и из него. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!NOTE]
>Этот соединитель поддерживает только копирование данных в API службы Azure Cosmos DB для MongoDB и из него. Сведения о SQL API см. в разделе [Соединитель API SQL Cosmos DB](connector-azure-cosmos-db.md). Другие типы API сейчас не поддерживаются.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из API службы Azure Cosmos DB для MongoDB в любое поддерживаемое хранилище данных, используемое в качестве приемника, или скопировать данные из любого исходного хранилища данных в API службы Azure Cosmos DB для MongoDB. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Возможности соединителя API службы Azure Cosmos DB для MongoDB:

- Копирование данных из API службы [Azure Cosmos DB для MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) и в него.
- Запись данных в Azure Cosmos DB с помощью операции **INSERT** или **UPSERT**.
- Импорт и экспорт документов JSON "как есть" либо копирование данных из набора табличных данных или в него. К примерам можно отнести базу данных SQL и CSV-файл. Сведения о копировании документов "как есть" в JSON-файлы или другую коллекцию Azure Cosmos DB либо из них см. в разделе "Импорт и экспорт документов JSON".

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые можно использовать для определения сущностей Фабрики данных, характерных для API службы Azure Cosmos DB для MongoDB.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы API Azure Cosmos DB для MongoDB поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** должно иметь значение **CosmosDbMongoDbApi**. | Да |
| connectionString |Укажите строку подключения для API службы Azure Cosmos DB для MongoDB. Вы найдете ее на портале Azure -> колонка Cosmos DB -> основная или дополнительная строка подключения с шаблоном `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных. Вы можете также [указать секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| database | Имя базы данных, к которой нужно получить доступ. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать Azure Integration Runtime или локальную среду IR (если хранилище данных расположено в частной сети). Если это свойство не задано, используется Azure Integration Runtime по умолчанию. |Нет |

**Пример**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md). Для набора данных API службы Azure Cosmos DB для MongoDB поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** набора данных должно иметь значение **CosmosDbMongoDbApiCollection**. |Да |
| collectionName |Имя коллекции Azure Cosmos DB. |Да |

**Пример**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником API службы Azure Cosmos DB для MongoDB.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Использование API службы Azure Cosmos DB для MongoDB в качестве источника

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** действия копирования должно иметь значение **CosmosDbMongoDbApiSource**. |Да |
| фильтр | Задает фильтр выбора с помощью операторов запросов. Чтобы получить все документы в коллекции, не указывайте этот параметр или передайте пустой документ ({}). | Нет |
| cursorMethods.project | Определяет, какие поля в документах для проекции необходимо получить. Чтобы получить все поля в соответствующих документах, не указывайте этот параметр. | Нет |
| cursorMethods.sort | Определяет, в каком порядке запрос будет возвращать соответствующие документы. См. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Нет |
| cursorMethods.limit | Определяет максимальное количество документов, возвращаемых сервером. См. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Нет | 
| cursorMethods.skip | Определяет, сколько документов нужно пропустить, прежде чем MongoDB начнет выдавать результаты. См. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Нет |
| batchSize | Определяет, сколько документов должно быть выдано в каждом пакете ответа от экземпляра MongoDB. В большинстве случаев изменение размера пакета не влияет на пользователя или приложение. В Cosmos DB размер пакета не может превышать 40 МБ — это сумма размеров всех документов в этом пакете, поэтому при работе с большими документами их количество нужно уменьшать. | Нет<br/>(значение по умолчанию — **100**) |

>[!TIP]
>ADF поддерживает прием документа BSON в **строгом режиме**. Убедитесь в том, что запрос фильтра находится в строгом режиме, а не в режиме оболочки. Более подробное описание см. в [руководстве MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Пример**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Использование API службы Azure Cosmos DB для MongoDB в качестве приемника

В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** приемника действия копирования должно иметь значение **CosmosDbMongoDbApiSink**. |Да |
| writeBehavior |Описывает способ записи данных в Azure Cosmos DB. Допустимые значения: **insert** и **upsert**.<br/><br/>Поведение **upsert** — замена документа, если документ с таким идентификатором уже существует. В противном выполняется вставка документа.<br /><br />**Примечание**. Фабрика данных автоматически создает идентификатор для документа, если идентификатор не указан в исходном документе или с помощью сопоставления столбцов. Это означает, что для правильной работы **upsert** у документа должен быть идентификатор. |Нет<br />(По умолчанию используется **insert**.) |
| writeBatchSize | Свойство **writeBatchSize** определяет размер документов для записи в каждом пакете. Вы можете увеличить значение **writeBatchSize** для повышения производительности или уменьшить значение, если документ большого размера. |Нет<br />(Значение по умолчанию — **10 000**.) |
| writeBatchTimeout | Время ожидания завершения операции пакетной вставки до истечения времени ожидания. Допустимое значение — TimeSpan. | Нет<br/>(по умолчанию используется **00:30:00** — 30 минут) |

**Пример**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Чтобы импортировать документы JSON без изменений, см. раздел [Импорт или экспорт документов JSON](#import-or-export-json-documents); чтобы копировать из данных в табличной форме, см. раздел [Сопоставление схем](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Импорт или экспорт документов JSON

Соединитель Azure Cosmos DB помогает легко:

* импортировать документы JSON из различных источников в Azure Cosmos DB, включая хранилище BLOB-объектов Azure, Azure Data Lake Store или другие файловые хранилища, поддерживаемые Фабрикой данных Azure;
* экспортировать документы JSON из коллекции Azure Cosmos DB в различные файловые хранилища;
* копировать документы между двумя коллекциями Azure Cosmos DB "как есть".

Для такого независимого от схемы копирования пропустите раздел "структура" (также называемый *схема*) в наборе данных и сопоставление схемы в действии копирования.

## <a name="schema-mapping"></a>Сопоставление схем

Чтобы скопировать данные из API службы Azure Cosmos DB для MongoDB в табличный приемник или наоборот, обратитесь к разделу [Сопоставление схем](copy-activity-schema-and-type-mapping.md#schema-mapping).

При записи в Cosmos DB, чтобы убедиться в том, что база данных Cosmos DB заполняется нужным идентификатором объекта из исходных данных (например, у вас есть столбец id в таблице базы данных SQL и вы хотите использовать это значение как идентификатор документа в MongoDB для вставки), необходимо задать правильное сопоставление схем в соответствии с определением строго режима MongoDB (`_id.$oid`) следующим образом.

![Сопоставление идентификатора в приемнике MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

После выполнения действия копирования следующий ObjectId BSON создается в качестве приемника.

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Дальнейшие действия

В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
