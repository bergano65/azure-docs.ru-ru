---
title: Копирование и преобразование данных в Azure Cosmos DB (API)
description: Узнайте, как копировать данные в Azure Cosmos DB и из нее (S'L API) и преобразовывать данные в Azure Cosmos DB (S'L API) с помощью Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: 7096b429145a54b5a09fe38eb8099c4ff24ac452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243618"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Копирование и преобразование данных в Azure Cosmos DB (API) с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-documentdb-connector.md)
> * [Текущая версия](connector-azure-cosmos-db.md)

В этой статье описано, как использовать действие копирования в Фабрике данных Azure, чтобы копировать данные в Azure Cosmos DB (API SQL) и оттуда, а также использовать Поток данных для преобразования данных в Azure Cosmos DB (API SQL). Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

>[!NOTE]
>Этот разъем поддерживает только Космический DB S'L API. Сведения о MongoDB см. в статье [Копирование данных в API службы Azure Cosmos DB для MongoDB или из него с помощью Фабрики данных Azure](connector-azure-cosmos-db-mongodb-api.md). Другие типы API сейчас не поддерживаются.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Azure Cosmos DB (S'L API) поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Для деятельности Copy этот разъем Azure Cosmos DB (S'L API) поддерживает:

- Копирование данных из службы Azure Cosmos DB и в нее с помощью [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Запись данных в Azure Cosmos DB с помощью операции **INSERT** или **UPSERT**.
- Импорт и экспорт документов JSON "как есть" либо копирование данных из набора табличных данных или в него. К примерам можно отнести базу данных SQL и CSV-файл. Чтобы скопировать документы как есть или из файлов JSON или в или из другой коллекции Azure Cosmos DB, см [Импорт и экспортировать документы JSON](#import-and-export-json-documents).

Фабрика данных интегрируется с [библиотекой массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started), чтобы обеспечить оптимальную производительность при операциях записи в Azure Cosmos DB.

> [!TIP]
> [Видео о переносе данных](https://youtu.be/5-SRNiC_qOU) поможет вам выполнить копирование данных из хранилища BLOB-объектов Azure в Azure Cosmos DB. Кроме того, в видео приведены общие рекомендации по настройке производительности приема данных в Azure Cosmos DB.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые можно использовать для определения сущностей фабрики данных, характерных для Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Cosmos DB (SQL API) поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение: **CosmosDb**. | Да |
| connectionString |Укажите сведения, необходимые для подключения к базе данных Azure Cosmos DB.<br />**Примечание**. Необходимо указать сведения о базе данных в строке подключения, как показано в приведенных ниже примерах. <br/> Вы можете также поместить ключ учетной записи в Azure Key Vault и извлечь конфигурацию `accountKey` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать Azure Integration Runtime или локальную среду IR (если хранилище данных расположено в частной сети). Если это свойство не задано, используется Azure Integration Runtime по умолчанию. |нет |

**Пример**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
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

Следующие свойства поддерживаются для набора данных Azure Cosmos DB (S'L API): 

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** набора данных должно быть установлено в **CosmosDbSqlApiCollection.** |Да |
| collectionName |Имя коллекции документов Azure Cosmos DB. |Да |

Если вы используете набор данных типа DocumentDbCollection, он по-прежнему поддерживается как для обратной совместимости для деятельности Copy and Lookup, он не поддерживается для потока данных. Предлагается использовать новую модель в будущем.

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

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Cosmos DB (SQL API). Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) как источник

Чтобы скопировать данные из Azure Cosmos DB (SQL API), в действии копирования в качестве типа **source** задайте значение **DocumentDbCollectionSource**. 

Следующие свойства поддерживаются в **разделе источник** копирования активности:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** источника активности копирования должно быть установлено на **CosmosDbSqlApiSource.** |Да |
| query |Укажите запрос Azure Cosmos DB для чтения данных.<br/><br/>Пример<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |нет <br/><br/>Если не указано, то выполняется инструкция SQL `select <columns defined in structure> from mycollection`. |
| предпочтительныеРегионы | Предпочтительный список регионов, к которые следует подключиться при извлечении данных из Cosmos DB. | нет |
| pageSize | Количество документов на страницу результата запроса. По умолчанию является "-1", что означает использование динамического размера динамической страницы стороны службы до 1000. | нет |

Если вы используете источник типа "DocumentDbCollectionSource", он по-прежнему поддерживается как для обратной совместимости. Предлагается использовать новую модель в будущем, которая предоставляет более широкие возможности для копирования данных из Cosmos DB.

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

При копировании данных из Cosmos DB, если вы не хотите [экспортировать документы JSON как есть,](#import-and-export-json-documents)наилучшей практикой является указание отображения в активности копирования. Фабрика данных выполняет значение отображения, указанное в деятельности, если строка не содержит значения для столбца, для значения столбца предусмотрено нулевое значение. Если не указано отображение, Data Factory вырисовывает схему, используя первую строку в данных. Если первая строка не содержит полной схемы, некоторые столбцы будут отсутствовать в результате операции активности.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) как приемник

Чтобы скопировать данные в Azure Cosmos DB (SQL API), в действии копирования задайте для типа **sink** значение **DocumentDbCollectionSink**. 

Следующие свойства поддерживаются в **разделе источник** копирования активности:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | **Свойство типа** раковины активности copy должно быть установлено к **CosmosDbSqlApiSink.** |Да |
| writeBehavior |Описывает способ записи данных в Azure Cosmos DB. Допустимые значения: **insert** и **upsert**.<br/><br/>Поведение **upsert** — замена документа, если документ с таким идентификатором уже существует. В противном выполняется вставка документа.<br /><br />**Примечание**. Фабрика данных автоматически создает идентификатор для документа, если идентификатор не указан в исходном документе или с помощью сопоставления столбцов. Это означает, что для правильной работы **upsert** у документа должен быть идентификатор. |нет<br />(По умолчанию используется **insert**.) |
| writeBatchSize | Фабрика данных использует [библиотеку массового исполнителя Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) для записи данных в Azure Cosmos DB. Свойство **writeBatchSize** контролирует размер документов, которые ADF предоставляет библиотеке. Вы можете увеличить значение **writeBatchSize** для повышения производительности или уменьшить значение, если документ большого размера. См. рекомендации ниже. |нет<br />(Значение по умолчанию — **10 000**.) |
| отключитьMetricsCollection | Data Factory собирает такие метрики, как Cosmos DB RUs для оптимизации производительности копий и рекомендаций. Если вы обеспокоены этим `true` поведением, укажите, чтобы выключить его. | Нет (значение по умолчанию — `false`) |

>[!TIP]
>Для импорта документов JSON как есть, обратитесь к [разделу документов импорта или экспорта JSON;](#import-and-export-json-documents) для копирования из табулярных данных, обратитесь к [Мигрировать из реляционной базы данных в Космос DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>В Cosmos DB размер одного запроса не должен превышать 2 МБ. Формула выглядит так: размер запроса = размера одного документа * размер пакета для записи. Если появится ошибка **Запрос слишком большой**, **уменьшите значение `writeBatchSize`** в конфигурации приемника действия копирования.

Если вы используете источник типа "DocumentDbCollectionSink", он по-прежнему поддерживается как для обратной совместимости. Предлагается использовать новую модель в будущем, которая предоставляет более широкие возможности для копирования данных из Cosmos DB.

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

### <a name="schema-mapping"></a>Сопоставление схем

Чтобы скопировать данные из Azure Cosmos DB для табликового погружения или обратного, обратитесь к [отображению схемы.](copy-activity-schema-and-type-mapping.md#schema-mapping)

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

При преобразовании данных в картографируем поток данных можно читать и писать в коллекции в Cosmos DB. Для получения дополнительной [source transformation](data-flow-source.md) информации [sink transformation](data-flow-sink.md) см.

### <a name="source-transformation"></a>Преобразование источника

Настройки, характерные для DB Azure Cosmos, доступны во вкладке **«Параметры исхода»** преобразования источника. 

**Включите системные столбцы:** Если это ```id``` ```_ts```правда, и другие системные столбцы будут включены в метаданные потока данных из CosmosDB. При обновлении коллекций важно включить его, чтобы можно было захватить существующий идентификатор строки.

**Размер страницы:** Количество документов на страницу результата запроса. По умолчанию является "-1", который использует динамическую страницу службы до 1000.

**Пропускная часть:** Установите дополнительное значение для количества RUs, которые вы хотели бы применить к сбору CosmosDB для каждого выполнения этого потока данных во время операции чтения. Минимум 400.

**Предпочтительные регионы:** Выберите предпочтительные области чтения для этого процесса.

#### <a name="json-settings"></a>Параметры JSON

**Единый документ:** Выберите этот параметр, если ADF будет рассматривать весь файл как единый документ JSON.

**Не цитируемые имена столбцов:** Выберите этот вариант, если имена столбцов в JSON не котируются.

**Имеет комментарии:** Используйте этот выбор, если в ваших документах JSON есть комментарии к данным.

**Одиночные цитаты:** Это должно быть выбрано, если столбцы и значения в документе котируются с помощью отдельных кавычек.

**Backslash бежал:** При использовании backslashes, чтобы избежать символов в вашем JSON, выберите этот вариант.

### <a name="sink-transformation"></a>Преобразование раковины

Настройки, характерные для DB Azure Cosmos, доступны во вкладке **«Настройки преобразования раковины».**

**Метод обновления:** Определяет, какие операции разрешены в пункте назначения базы данных. По умолчанию разрешаются только вставки. Для обновления, обновления или удаления строк требуется преобразование строки alter-row для тегов строк для этих действий. Для обновления, upserts и удаляет, ключевые столбцы или столбцы должны быть установлены, чтобы определить, какой ряд изменить.

**Действие коллекции:** Определяет, следует ли воссоздать коллекцию назначения до написания.
* Нет: Никакие действия не будут сделаны для коллекции.
* Воссоздать: Коллекция будет удалена и воссоздана

**Размер пакета**: Контролирует, сколько строк пишется в каждом ведре. Большие размеры пакетов улучшают сжатие и оптимизацию памяти, но рискуют из-за исключений памяти при кэшировать данные.

**Ключ раздела:** Введите строку, представляющую ключ раздела для вашей коллекции. Например, ```/movies/title```.

**Пропускная часть:** Установите дополнительное значение для количества RUs, которые вы хотели бы применить к вашей коллекции CosmosDB для каждого выполнения этого потока данных. Минимум 400.

**Написать бюджет пропускной записи:** Целый ряд, который представляет количество RUs, которые вы хотите выделить для выполнения задания Spark с навалом. Это число не от общего объема пропускной связи, выделенной для сбора.

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="import-and-export-json-documents"></a>Документы по импорту и экспорту JSON

Соединитель Azure Cosmos DB (SQL API) помогает легко:

* копировать документы между двумя коллекциями Azure Cosmos DB "как есть".
* импортировать документы JSON из различных источников в Azure Cosmos DB, включая хранилище BLOB-объектов Azure, Azure Data Lake Store или другие файловые хранилища, поддерживаемые Фабрикой данных Azure;
* экспортировать документы JSON из коллекции Azure Cosmos DB в различные файловые хранилища;

Чтоб обеспечить копирование без использования схем, сделайте следующее.

* При использовании инструмента "Копирование данных" установите флажок **Export as-is to JSON files or DocumentDB collection** (Экспортировать "как есть" в JSON-файлы или коллекцию Cosmos DB).
* При использовании автором действия выберите формат JSON с соответствующим хранилищем файлов для источника или раковины.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Миграция из реляционной базы данных в Космос DB

При переходе из реляционной базы данных, например, сервера S'L в Azure Cosmos DB, активность копирования может легко отображать табулярные данные из источника для выравнивания документов JSON в Cosmos DB. В некоторых случаях может потребоваться редизайн модели данных, чтобы оптимизировать ее для случаев использования НоСЗЛ в соответствии с [моделированием данных в Azure Cosmos DB,](../cosmos-db/modeling-data.md)например, для денормализации данных путем встраивания всех связанных субэлементов в один документ JSON. В этом случае обратитесь к [этой статье](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) с пошаговым материалом о том, как ее достичь с помощью копирования Azure Data Factory.

## <a name="next-steps"></a>Дальнейшие действия

В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
