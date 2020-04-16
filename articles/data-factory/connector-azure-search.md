---
title: Копирование данных в индекс поиска
description: Узнайте, как отправлять или копировать данные в индекс службы "Поиск Azure" с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418241"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Копирование данных в индекс когнитивного поиска Azure с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-azure-search-connector.md)
> * [Текущая версия](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается, как использовать активность копирования в Фабрике данных Azure для копирования данных в индекс когнитивного поиска Azure. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из любого поддерживаемого хранилища исходных данных в индекс поиска. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Data Factory, специфичных для разъема Azure Cognitive Search.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы связанного с Azure Cognitive Search:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzureSearch**. | Да |
| url | URL для поисковой службы. | Да |
| ключ | Ключ от админ для поисковой службы. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

> [!IMPORTANT]
> При копировании данных из облачного хранилища данных в поисковый индекс в связанном с Azure Cognitive Search сервисе необходимо передать Runtime интеграции Azure с явным регионом в connactVia. Установите регион как тот, в котором находится служба поиска. Дополнительные сведения о среде IR Azure см. [здесь](concepts-integration-runtime.md#azure-integration-runtime).

**Примере:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе приводится список свойств, поддерживаемых набором данных Azure Cognitive Search.

Для копирования данных в Azure Cognitive Search поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение: **AzureSearchIndex**. | Да |
| indexName | Название поискового индекса. Фабрика данных не создает индекс. Индекс должен существовать в Azure Cognitive Search. | Да |

**Примере:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником когнитивного поиска Azure.

### <a name="azure-cognitive-search-as-sink"></a>Azure Когнитивный поиск как раковина

Чтобы скопировать данные в Azure Cognitive Search, установите тип исхода в активности копирования на **AzureSearchIndexSink.** Следующие свойства поддерживаются в разделе **раковины активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **AzureSearchIndexSink**. | Да |
| writeBehavior | Указывает действие (объединение или замена), выполняемое, если документ уже существует в индексе. Ознакомьтесь с разделом [Свойство WriteBehavior](#writebehavior-property).<br/><br/>Допустимые значения: **Объединить** (по умолчанию) и **Отправить**. | нет |
| writeBatchSize | Загружает данные в индекс поиска, когда размер буфера достигает writeBatchSize. Ознакомьтесь с разделом [Свойство WriteBatchSize](#writebatchsize-property).<br/><br/>Допустимые значения: целые числа от 1 до 1000; значение по умолчанию — 1000. | нет |

### <a name="writebehavior-property"></a>Свойство WriteBehavior

При записи данных AzureSearchSink выполняет операцию upsert. Другими словами, при написании документа, если ключ документа уже существует в индексе поиска, Azure Cognitive Search обновляет существующий документ, а не бросает исключение из конфликта.

AzureSearchSink проявляет два типа поведения upsert (с использованием пакета SDK AzureSearch):

- **Объединение**. Все столбцы в новом документе объединяются со столбцами в существующем. Для столбцов с значением null в новом документе значение столбцов в существующем документе сохраняется.
- **Отправка**. Новый документ заменяет существующий. Для столбцов, не указанных в новом документе, задается значение null независимо от того, есть ли в существующем документе столбцы с значением, отличным от null.

Поведение по умолчанию — **объединение**.

### <a name="writebatchsize-property"></a>Свойство WriteBatchSize

Сервис Azure Cognitive Search поддерживает написание документов как пакет. Пакет может содержать от 1 до 1000 действий. Действие обрабатывает один документ для выполнения операции передачи или объединения.

**Примере:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Поддержка типов данных

В следующей таблице указывается, поддерживается тип данных Azure Cognitive Search или нет.

| Тип данных когнитивного поиска Azure | Поддерживается в Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Строка | Да |
| Int32 | Да |
| Int64 | Да |
| Double | Да |
| Логическое | Да |
| DataTimeOffset | Да |
| Массив строк | Нет |
| GeographyPoint | Нет |

В настоящее время другие типы данных, например ComplexType, не поддерживаются. Полный список поддерживаемых типами данных Azure Cognitive Search [см.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
