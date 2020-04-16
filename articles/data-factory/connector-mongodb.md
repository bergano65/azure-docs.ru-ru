---
title: Копия данных из MongoDB
description: Узнайте, как копировать данные из MongoDB на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 3b6476b794d2e1b2e9a36aa26f35c247641d44e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418156"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Копирование данных из MongoDB с помощью фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных MongoDB. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!IMPORTANT]
>ADF выпустил новую версию соединителя MongoDB, которая обеспечивает более эффективную поддержку MongoDB. При использовании предыдущей соединителя MongoDB в решении, которое поддерживается как есть, для обеспечения обратной совместимости см. статью [Соединитель MongoDB (для прежних версий)](connector-mongodb-legacy.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из базы данных MongoDB можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель MongoDB поддерживает **версии до 3.4 включительно**.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю MongoDB.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы MongoDB поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type |Свойство типа должно быть установлено на: **MongoDbV2** |Да |
| connectionString |Укажите строку подключения MongoDB, например `mongodb://[username:password@]host[:port][/[database][?options]]`. Дополнительные сведения см. в [руководстве MongoDB по строке подключения](https://docs.mongodb.com/manual/reference/connection-string/). <br/><br /> Вы также можете поместить пароль в Хранилище `password` ключей Azure и вытащить конфигурацию из строки соединения. С более подробной информацией обратитесь к [учетным данным Хранилища в Хранилище ключей Azure.](store-credentials-in-key-vault.md) |Да |
| База данных | Имя базы данных, к которой нужно получить доступ. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

**Примере:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md). Набор данных MongoDB поддерживает следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено: **MongoDbV2Collection** | Да |
| collectionName |Имя коллекции в базе данных MongoDB |Да |

**Примере:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником MongoDB.

### <a name="mongodb-as-source"></a>MongoDB в качестве источника

Следующие свойства поддерживаются в разделе **источника активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено: **MongoDbV2Источник** | Да |
| фильтр | Задает фильтр выбора с помощью операторов запросов. Чтобы получить все документы в коллекции, не указывайте этот параметр или передайте пустой документ ({}). | нет |
| cursorMethods.project | Определяет, какие поля в документах для проекции необходимо получить. Чтобы получить все поля в соответствующих документах, не указывайте этот параметр. | нет |
| cursorMethods.sort | Определяет, в каком порядке запрос будет возвращать соответствующие документы. См. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | нет |
| cursorMethods.limit | Определяет максимальное количество документов, возвращаемых сервером. См. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | нет |
| cursorMethods.skip | Определяет, сколько документов нужно пропустить, прежде чем MongoDB начнет выдавать результаты. См. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | нет |
| batchSize | Определяет, сколько документов должно быть выдано в каждом пакете ответа от экземпляра MongoDB. В большинстве случаев изменение размера пакета не влияет на пользователя или приложение. В Cosmos DB размер пакета не может превышать 40 МБ — это сумма размеров всех документов в этом пакете, поэтому при работе с большими документами их количество нужно уменьшать. | нет<br/>(по умолчанию **100)** |

>[!TIP]
>ADF поддерживает прием документа BSON в **строгом режиме**. Убедитесь в том, что запрос фильтра находится в строгом режиме, а не в режиме оболочки. Более подробное описание см. в [руководстве MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>Экспорт документов JSON без изменений

Соединитель MongoDB можно использовать для экспорта документов JSON из коллекции MongoDB в различные файловые хранилища или Azure Cosmos DB без изменений. Для такого независимого от схемы копирования пропустите раздел "структура" (также называемый *схема*) в наборе данных и сопоставление схемы в действии копирования.

## <a name="schema-mapping"></a>Сопоставление схем

Инструкции по копированию данных из MongoDB в табличный приемник см. в разделе о [сопоставлении схем](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
