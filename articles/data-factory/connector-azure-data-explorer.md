---
title: Копирование данных в Azure Data Explorer или из
description: Узнайте, как копировать данные из Azure Data Explorer или обратно с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382766"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Копирование данных в Explorer данных Azure с помощью Azure Data Factory

В этой статье описывается, как использовать активность копирования на фабрике данных Azure для копирования данных в [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)или из. Он основывается на статье [обзора активности копирования,](copy-activity-overview.md) которая предлагает общий обзор активности копирования.

>[!TIP]
>Для интеграции Azure Data Factory и Интеграции Azure Data Explorer в целом узнайте больше из [интеграции Azure Data Explorer с Azure Data Factory.](/azure/data-explorer/data-factory-integration)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Azure Data Explorer поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из любого поддерживаемого хранилища исходных данных в Azure Data Explorer. Данные из базы данных Azure Data Explorer также можете скопировать в любое хранилище данных, поддерживаемое приемником. Для списка хранилиров данных, поддерживаемых в качестве источников или поглотителей, [см.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Копирование данных в Azure Data Explorer через магазин данных с помощью автономной интеграции поддерживается в версии 3.14 и позже.

С разъемом Azure Data Explorer вы можете сделать следующее:

* копировать данные с помощью проверки подлинности токена приложения Azure Active Directory (Azure AD) с **субъектом-службой**;
* извлекать данные с помощью запроса KQL (Kusto) в качестве источника;
* добавлять данные в таблицу назначения в качестве приемника.

## <a name="getting-started"></a>Начало работы

>[!TIP]
>Для получения пошагового промеля лазурного разъема Explorer Данных смотрите [копирование данных в/из Azure Data Explorer с помощью Azure Data Factory](/azure/data-explorer/data-factory-load-data) и [массовой копии из базы данных в Azure Data Explorer.](/azure/data-explorer/data-factory-template)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Azure Data Explorer.

## <a name="linked-service-properties"></a>Свойства связанной службы

Разъем Azure Data Explorer использует основную аутентификацию службы. Выполните следующие действия, чтобы получить основной службы и предоставить разрешения:

1. Зарегистрируйте сущность приложения в каталоге Azure Active, выяснив шаги в [регистрации приложения с помощью клиента Azure AD.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Запишите следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Предоставьте директору службы правильные разрешения в Azure Data Explorer. Просрнять [разрешения базы данных Manage Azure Data Explorer](/azure/data-explorer/manage-database-permissions) для получения подробной информации о ролях и разрешениях и об управлении разрешениями. В общем, вы должны:

    - **В качестве источника**, предоставить по крайней мере роль **просмотра базы данных** в базу данных
    - **Как раковина**, предоставить по крайней мере роль **глотателя базы данных** в вашу базу данных

>[!NOTE]
>При использовании пользовательского интерфейса Data Factory для автора учетная запись пользователя для входа используется для рассылки кластеров, баз данных и таблиц Azure Data Explorer. Вручную введите имя, если у вас нет разрешения на эти операции.

Следующие свойства поддерживаются для службы, связанной с Azure Data Explorer:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** должно быть установлено на **AzureDataExplorer.** | Да |
| endpoint | URL-адрес конечной точки кластера Azure Data Explorer в формате `https://<clusterName>.<regionName>.kusto.windows.net`. | Да |
| База данных | Имя базы данных. | Да |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Это известно как "Authority ID" в [строке соединения Kusto.](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) Извлеките его, зависая указателем мыши в правом верхнем углу портала Azure. | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. Это известно как "AAD приложение клиента ID" в [строке соединения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Да |
| servicePrincipalKey | Укажите ключ приложения. Это известно как "ключ приложения AAD" в [строке соединения Kusto.](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) Отметьте это поле как **SecureString** для безопасного хранения на фабрике данных или [ссылку на безопасные данные, хранящиеся в Azure Key Vault.](store-credentials-in-key-vault.md) | Да |

**Пример свойств связанных услуг:**

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

Полный список разделов и свойств, доступных для определения наборов данных, [см.](concepts-datasets-linked-services.md) В этом разделе перечислены свойства, которые поддерживает набор данных Azure Data Explorer.

Чтобы скопировать данные в Azure Data Explorer, укажите для свойства type значение набора данных **AzureDataExplorerTable**.

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** должно быть установлено на **AzureDataExplorerTable.** | Да |
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

Полный список разделов и свойств, [Pipelines and activities in Azure Data Factory](concepts-pipelines-activities.md)доступных для определения деятельности, см. В этом разделе приводится список свойств, которые поддерживают и поддерживают ресурсы Azure Data Explorer.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer в качестве источника

Чтобы скопировать данные Azure Data Explorer, задайте для свойства **type** в источнике действия копирования значение **AzureDataExplorerSource**. Следующие свойства поддерживаются в разделе **источника активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** источника активности копирования должно быть установлено: **AzureDataExplorerSource** | Да |
| query | Запрос только для чтения в [формате KQL](/azure/kusto/query/). Используйте пользовательский запрос KQL в качестве ссылки. | Да |
| queryTimeout | Время ожидания до того, как запрос запроса выйдет из-под засевыходит. Значение по умолчанию составляет 10 мин (00:10:00); разрешенное максимальное значение составляет 1 час (01:00:00). | нет |
| noTruncation | Указывает, следует ли усеивать набор возвращенных результатов. По умолчанию результат усечен после 500 000 записей или 64 мегабайт (МБ). Truncation настоятельно рекомендуется для обеспечения правильного поведения деятельности. |нет |

>[!NOTE]
>По умолчанию источник Azure Data Explorer имеет ограничение по размеру 500 000 записей или 64 МБ. Чтобы получить все записи без усечения, можно указать `set notruncation;` в начале запроса. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/kusto/concepts/querylimits)

**Примере:**

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

Чтобы скопировать данные Azure Data Explorer, задайте для свойства type в приемнике действия копирования значение **AzureDataExplorerSink**. Следующие свойства поддерживаются в разделе **раковины активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | **Свойство типа** раковины активности копирования должно быть установлено: **AzureDataExplorerSink**. | Да |
| ingestionMappingName | Название заранее созданного [отображения](/azure/kusto/management/mappings#csv-mapping) на столе Kusto. Для отображения столбцов от источника до Azure Data Explorer (что относится ко [всем поддерживаемым источникам и форматам,](copy-activity-overview.md#supported-data-stores-and-formats)включая форматы CSV/JSON/Avro), можно использовать [отображение столбца](copy-activity-schema-and-type-mapping.md) активности копирования (неявно по имени или явно настроенному) и/или отображение данных Azure Data Explorer. | нет |
| additionalСвойства | Пакет свойств, который может быть использован для указания любого из свойств приема, которые еще не установлены лазурным потоком. В частности, он может быть полезен для указания тегов приема. Узнайте больше из [данных Azure Data Исследуйте документ о проглатываниях данных.](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html) | нет |

**Примере:**

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
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

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Для получения дополнительной информации [Lookup activity](control-flow-lookup-activity.md)о свойствах см.

## <a name="next-steps"></a>Следующие шаги

* Для списка хранилищ данных, которые активность копирования в Azure Data Factory поддерживается в качестве источников и поглотителей, [см.](copy-activity-overview.md#supported-data-stores-and-formats)

* Узнайте больше о том, как [копировать данные с фабрики данных Azure в Azure Data Explorer.](/azure/data-explorer/data-factory-load-data)
