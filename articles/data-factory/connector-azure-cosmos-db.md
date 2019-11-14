---
title: Копирование и преобразование данных в Azure Cosmos DB (API SQL) с помощью фабрики данных
description: Узнайте, как копировать данные в Azure Cosmos DB (API SQL) и из них, а затем преобразовывать данные в Azure Cosmos DB (API SQL) с помощью фабрики данных.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 5e9db7c63e1493e1de5593262515040f071186e8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076795"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Копирование и преобразование данных в Azure Cosmos DB (API SQL) с помощью фабрики данных Azure

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-documentdb-connector.md)
> * [Текущая версия](connector-azure-cosmos-db.md)

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные в Azure Cosmos DB (API SQL) и использовать поток данных для преобразования данных в Azure Cosmos DB (API SQL). Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

>[!NOTE]
>Этот соединитель поддерживает только Cosmos DB API SQL. Сведения о MongoDB см. в статье [Копирование данных в API службы Azure Cosmos DB для MongoDB или из него с помощью Фабрики данных Azure](connector-azure-cosmos-db-mongodb-api.md). Другие типы API сейчас не поддерживаются.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Azure Cosmos DB (API SQL) поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Поток данных сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Для действия копирования этот соединитель Azure Cosmos DB (API SQL) поддерживает:

- Копирование данных из службы Azure Cosmos DB и в нее с помощью [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Запись данных в Azure Cosmos DB с помощью операции **INSERT** или **UPSERT**.
- Импорт и экспорт документов JSON "как есть" либо копирование данных из набора табличных данных или в него. К примерам можно отнести базу данных SQL и CSV-файл. Сведения о копировании документов "как есть" в JSON-файлы или другую коллекцию Azure Cosmos DB либо из них см. в разделе "Импорт и экспорт документов JSON".

Фабрика данных интегрируется с [библиотекой массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started), чтобы обеспечить оптимальную производительность при операциях записи в Azure Cosmos DB.

> [!TIP]
> [Видео о переносе данных](https://youtu.be/5-SRNiC_qOU) поможет вам выполнить копирование данных из хранилища BLOB-объектов Azure в Azure Cosmos DB. Кроме того, в видео приведены общие рекомендации по настройке производительности приема данных в Azure Cosmos DB.

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые можно использовать для определения сущностей фабрики данных, характерных для Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Cosmos DB (SQL API) поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение: **CosmosDb**. | Yes |
| connectionString |Укажите сведения, необходимые для подключения к базе данных Azure Cosmos DB.<br />**Примечание**. Необходимо указать сведения о базе данных в строке подключения, как показано в приведенных ниже примерах. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить ключ учетной записи в Azure Key Vault и извлечь конфигурацию `accountKey` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать Azure Integration Runtime или локальную среду IR (если хранилище данных расположено в частной сети). Если это свойство не задано, используется Azure Integration Runtime по умолчанию. |Нет |

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

**Пример: ключ учетной записи хранения в Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md).

Для набора данных Azure Cosmos DB (SQL API) поддерживаются следующие свойства: 

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Свойство **Type** набора данных должно иметь значение **космосдбсклапиколлектион**. |Yes |
| collectionName |Имя коллекции документов Azure Cosmos DB. |Yes |

Если используется тип данных "DocumentDbCollection", он по-прежнему поддерживается как есть для обратной совместимости операций копирования и поиска, но не поддерживается для потока данных. Рекомендуется использовать новую модель.

**Пример**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Схема фабрики данных

Для хранилищ данных без схемы, таких как Azure Cosmos DB, действие копирования определяет схему одним из приведенных ниже способов. Если только вам не требуется [импортировать или экспортировать документы JSON "как есть"](#import-or-export-json-documents), мы рекомендуем указывать структуру данных в разделе **structure**.

Фабрика данных учитывает сопоставление, указанное для действия. Если строка не содержит значение столбца, ему присваивается значение NULL.

Если сопоставление не указано, служба фабрики данных определяет схему с помощью первой строки данных. Если первая строка не содержит полную схему, некоторые столбцы будут отсутствовать в результате операции действия.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Cosmos DB (SQL API).

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) как источник

Чтобы скопировать данные из Azure Cosmos DB (SQL API), в действии копирования в качестве типа **source** задайте значение **DocumentDbCollectionSource**. 

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Свойство **Type** источника действия копирования должно иметь значение **космосдбсклаписаурце**. |Yes |
| запрос |Укажите запрос Azure Cosmos DB для чтения данных.<br/><br/>Пример:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Нет <br/><br/>Если не указано, то выполняется инструкция SQL `select <columns defined in structure> from mycollection`. |
| преферредрегионс | Предпочтительный список регионов для подключения при получения данных из Cosmos DB. | Нет |
| pageSize | Число документов на страницу результата запроса. Значение по умолчанию — "-1", что означает использование динамического размера страницы на стороне службы до 1000. | Нет |

Если используется источник типов "DocumentDbCollectionSource", он по-прежнему поддерживается как есть для обратной совместимости. Рекомендуется использовать новую модель, которая пересылается, чтобы обеспечить более широкие возможности копирования данных из Cosmos DB.

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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
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

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Свойство **Type** приемника действия копирования должно иметь значение **космосдбсклаписинк**. |Yes |
| writeBehavior |Описывает способ записи данных в Azure Cosmos DB. Допустимые значения: **insert** и **upsert**.<br/><br/>Поведение **upsert** — замена документа, если документ с таким идентификатором уже существует. В противном выполняется вставка документа.<br /><br />**Примечание**. Фабрика данных автоматически создает идентификатор для документа, если идентификатор не указан в исходном документе или с помощью сопоставления столбцов. Это означает, что для правильной работы **upsert** у документа должен быть идентификатор. |Нет<br />(По умолчанию используется **insert**.) |
| writeBatchSize | Фабрика данных использует [библиотеку массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) для записи данных в Azure Cosmos DB. Свойство **writeBatchSize** определяет размер документов, которые ADF передает в библиотеку. Вы можете увеличить значение **writeBatchSize** для повышения производительности или уменьшить значение, если документ большого размера. См. рекомендации ниже. |Нет<br />(Значение по умолчанию — **10 000**.) |
| дисаблеметриксколлектион | Фабрика данных собирает такие метрики, как Cosmos DB RUs для оптимизации производительности и рекомендаций по копированию. Если вы отвечаете за такое поведение, укажите `true`, чтобы отключить его. | Нет (значение по умолчанию — `false`) |

>[!TIP]
>В Cosmos DB размер одного запроса не должен превышать 2 МБ. Формула выглядит так: размер запроса = размера одного документа * размер пакета для записи. Если появится ошибка **Запрос слишком большой**, **уменьшите значение `writeBatchSize`** в конфигурации приемника действия копирования.

Если используется источник типов "DocumentDbCollectionSink", он по-прежнему поддерживается как есть для обратной совместимости. Рекомендуется использовать новую модель, которая пересылается, чтобы обеспечить более широкие возможности копирования данных из Cosmos DB.

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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

Дополнительные сведения см. в статье преобразование [источника](data-flow-source.md) и [Преобразование приемника](data-flow-sink.md) в потоке данных сопоставления.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Импорт или экспорт документов JSON

Соединитель Azure Cosmos DB (SQL API) помогает легко:

* импортировать документы JSON из различных источников в Azure Cosmos DB, включая хранилище BLOB-объектов Azure, Azure Data Lake Store или другие файловые хранилища, поддерживаемые Фабрикой данных Azure;
* экспортировать документы JSON из коллекции Azure Cosmos DB в различные файловые хранилища;
* копировать документы между двумя коллекциями Azure Cosmos DB "как есть".

Чтоб обеспечить копирование без использования схем, сделайте следующее.

* При использовании инструмента "Копирование данных" установите флажок **Export as-is to JSON files or DocumentDB collection** (Экспортировать "как есть" в JSON-файлы или коллекцию Cosmos DB).
* При использовании создания действия выберите формат JSON с соответствующим хранилищем файлов для источника или приемника.

## <a name="next-steps"></a>Дополнительная информация

В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
