---
title: Копирование данных из Azure Data Explorer или обратно с помощью Фабрики данных Azure
description: Узнайте, как копировать данные из Azure Data Explorer или обратно с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 6945e4dcf6baf44881bd5b13571dd03e3dee41ed
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300543"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Копирование данных в Azure обозреватель данных или из нее с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в [Обозреватель данных Azure](../data-explorer/data-explorer-overview.md)или из нее. Она основана на статье [Обзор действия копирования](copy-activity-overview.md) , которая предоставляет общий обзор действия копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Azure обозреватель данных поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из любого поддерживаемого хранилища исходных данных в Azure Data Explorer. Данные из базы данных Azure Data Explorer также можете скопировать в любое хранилище данных, поддерживаемое приемником. Список хранилищ данных, поддерживаемых действием копирования в качестве источников или приемников, см. в таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>Копирование данных в обозреватель данных Azure или из него через локальное хранилище данных с помощью локальной среды выполнения интеграции поддерживается в версии 3,14 и более поздних.

С помощью соединителя Azure обозреватель данных можно выполнить следующие действия.

* копировать данные с помощью проверки подлинности токена приложения Azure Active Directory (Azure AD) с **субъектом-службой**;
* извлекать данные с помощью запроса KQL (Kusto) в качестве источника;
* добавлять данные в таблицу назначения в качестве приемника.

## <a name="getting-started"></a>Приступая к работе

>[!TIP]
>Пошаговое руководство по соединителю обозреватель данных Azure см. в статье [копирование данных в azure обозреватель данных и обратно с помощью фабрики данных Azure](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Azure Data Explorer.

## <a name="linked-service-properties"></a>Свойства связанной службы

Соединитель Azure обозреватель данных использует проверку подлинности субъекта-службы. Выполните следующие действия, чтобы получить субъект-службу и предоставить разрешения.

1. Зарегистрируйте сущность приложения в Azure Active Directory, выполнив действия, описанные в статье [Регистрация приложения в клиенте Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Запишите следующие значения, которые используются для определения связанной службы:

    - ИД приложения
    - Ключ приложения
    - Идентификатор клиента

2. Предоставьте субъекту-службе правильные разрешения в Azure обозреватель данных. Подробные сведения о ролях и разрешениях и об управлении разрешениями см. в статье [Управление разрешениями базы данных Azure обозреватель данных](../data-explorer/manage-database-permissions.md) . Как правило, необходимо:

    - В **качестве источника**предоставьте по крайней мере роль **Database Viewer** для базы данных.
    - В **качестве приемника**предоставьте по крайней мере роль **приема базы данных** для базы данных.

>[!NOTE]
>При использовании пользовательского интерфейса фабрики данных для создания списка кластеров, баз данных и таблиц Azure обозреватель данных используется учетная запись пользователя для входа. Введите имя вручную, если у вас нет разрешения на выполнение этих операций.

Для связанной службы обозреватель данных Azure поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства **Type** необходимо задать значение **азуредатаексплорер**. | Да |
| endpoint | URL-адрес конечной точки кластера Azure Data Explorer в формате `https://<clusterName>.<regionName>.kusto.windows.net`. | Да |
| database | Имя базы данных. | Да |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Это называется "ИД центра сертификации" в [строке подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Извлеките его, наведя указатель мыши в правом верхнем углу портал Azure. | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. Это называется "идентификатор клиента приложения AAD" в [строке подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Да |
| servicePrincipalKey | Укажите ключ приложения. Это называется "ключ приложения AAD" в [строке подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Пометьте это поле как **SecureString** , чтобы безопасно хранить его в фабрике данных, или [сослаться на защищенные данные, хранящиеся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |

**Пример свойств связанной службы:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. [в статье DataSets in Azure Data фабрика](concepts-datasets-linked-services.md). В этом разделе перечислены свойства, поддерживаемые набором данных Azure обозреватель данных.

Чтобы скопировать данные в Azure Data Explorer, укажите для свойства type значение набора данных **AzureDataExplorerTable**.

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства **Type** необходимо задать значение **азуредатаексплорертабле**. | Да |
| table | Имя таблицы, на которое ссылается связанная служба. | "Да" для приемника, "Нет" для источника |

**Пример свойств набора данных:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. [в статье конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, которые поддерживаются источниками и приемниками Azure обозреватель данных.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer в качестве источника

Чтобы скопировать данные Azure Data Explorer, задайте для свойства **type** в источнике действия копирования значение **AzureDataExplorerSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству **type** источника действия копирования необходимо задать значение **AzureDataExplorerSource** | Да |
| query | Запрос только для чтения в [формате KQL](/azure/kusto/query/). Используйте пользовательский запрос KQL в качестве ссылки. | Да |
| queryTimeout | Время ожидания до истечения срока запроса. Значение по умолчанию — 10 мин. (00:10:00); допустимое максимальное значение — 1 час (01:00:00). | Нет |
| noTruncation | Указывает, следует ли усекать возвращенный результирующий набор. По умолчанию результат усекается после 500 000 записей или 64 мегабайт (МБ). Для обеспечения правильного поведения действия настоятельно рекомендуется усечение. |Нет |

>[!NOTE]
>По умолчанию источник обозреватель данных Azure имеет ограничение размера 500 000 записей или 64 МБ. Чтобы получить все записи без усечения, можно указать `set notruncation;` в начале запроса. Дополнительные сведения см. в разделе [ограничения запросов](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer в качестве приемника

Чтобы скопировать данные Azure Data Explorer, задайте для свойства type в приемнике действия копирования значение **AzureDataExplorerSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству **type** приемника действия копирования необходимо присвоить значение **Азуредатаексплорерсинк**. | Да |
| ingestionMappingName | Имя предварительно созданного [сопоставления](/azure/kusto/management/mappings#csv-mapping) в таблице Kusto. Чтобы сопоставить столбцы из источника и обозреватель данных Azure (которые применяются ко [всем поддерживаемым исходным хранилищам и форматам](copy-activity-overview.md#supported-data-stores-and-formats), включая форматы CSV/JSON/Avro), можно использовать [сопоставление столбцов](copy-activity-schema-and-type-mapping.md) действия копирования (неявно по имени или явно настроенное) и сопоставления обозреватель данных Azure или VSPS. | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Дополнительные сведения о свойствах см. в разделе [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Следующие шаги

* Список хранилищ данных, которые действие копирования в фабрике данных Azure поддерживает в качестве источников и приемников, см. в разделе [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats).

* Узнайте больше о том, как [Копировать данные из фабрики данных Azure в обозреватель данных Azure](/azure/data-explorer/data-factory-load-data).
