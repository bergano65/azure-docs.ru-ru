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
ms.date: 04/16/2019
ms.author: orspodek
ms.openlocfilehash: f501257903f3b7c621512f06d1c8c7109e22db1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394512"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Копирование данных из Azure Data Explorer или обратно с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure копировать данные из [Azure Data Explorer](../data-explorer/data-explorer-overview.md) или обратно. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из любого поддерживаемого хранилища исходных данных в Azure Data Explorer. Данные из базы данных Azure Data Explorer также можете скопировать в любое хранилище данных, поддерживаемое приемником. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md).

>[!NOTE]
>Копирование данных из обозревателя данных Azure в на локальное хранилище данных с помощью локальной среды выполнения интеграции, поддерживается начиная с версии 3,14.

Соединитель Azure Data Explorer позволяет выполнять следующие действия:

* копировать данные с помощью проверки подлинности токена приложения Azure Active Directory (Azure AD) с **субъектом-службой**;
* извлекать данные с помощью запроса KQL (Kusto) в качестве источника;
* добавлять данные в таблицу назначения в качестве приемника.

## <a name="getting-started"></a>Приступая к работе

>[!TIP]
>Пошаговое руководство по с помощью соединителя обозреватель данных Azure, см. в разделе [копирования данных с помощью обозревателя данных Azure с помощью фабрики данных Azure](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Azure Data Explorer.

## <a name="linked-service-properties"></a>Свойства связанной службы

Соединитель обозреватель данных Azure использует проверку подлинности субъекта-службы. Выполните следующие действия, чтобы получить субъект-службу и предоставить разрешения.

1. Чтобы зарегистрировать сущность приложения в Azure Active Directory (Azure AD), необходимо следовать указаниям из раздела [Регистрация приложения в клиенте Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Запишите следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Предоставьте разрешение субъекта-службы правильной в обозреватель данных Azure. Ссылаться на [разрешения базы данных управления обозревателя данных Azure](../data-explorer/manage-database-permissions.md) с подробными сведениями о роли и разрешения, а также пошаговые указания по Управление разрешениями. Как правило вам нужно

    - **Как источник**, назначьте как минимум **базы данных просмотра** роли к базе данных.
    - **Как приемник**, назначьте как минимум **базы данных это** роли к базе данных.

>[!NOTE]
>При использовании пользовательского интерфейса фабрики данных AZURE для создания, операции, получение списка баз данных в связанной службе или список таблиц в наборе данных может потребоваться более привилегированным предоставлены разрешения для субъекта-службы. Кроме того вы можете вручную ввести имя базы данных и имя таблицы. Скопируйте works выполнение действия до тех пор, пока участнику службы предоставляется с предоставлено правильное разрешение на чтение и запись данных.

Для связанной службы Azure Data Explorer поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству **type** необходимо задать значение **AzureDataExplorer** | Yes |
| endpoint | URL-адрес конечной точки кластера Azure Data Explorer в формате `https://<clusterName>.<regionName>.kusto.windows.net`. | Yes |
| database | Имя базы данных. | Yes |
| клиент | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Это связано с тем, что обычно знаете как "**Идентификации**" в [строку подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Получите его, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. Это связано с тем, что обычно знаете как "**идентификатор клиента приложения AAD**" в [строку подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Yes |
| servicePrincipalKey | Укажите ключ приложения. Это связано с тем, что обычно знаете как "**ключ приложения AAD**" в [строку подключения Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Пример свойств связанной службы**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных Azure Data Explorer.

Чтобы скопировать данные в Azure Data Explorer, укажите для свойства type значение набора данных **AzureDataExplorerTable**.

Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству **type** необходимо присвоить значение **AzureDataExplorerTable** | Yes |
| таблица | Имя таблицы, на которое ссылается связанная служба. | "Да" для приемника, "Нет" для источника |

**Пример свойств набора данных**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Data Explorer.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer в качестве источника

Чтобы скопировать данные Azure Data Explorer, задайте для свойства **type** в источнике действия копирования значение **AzureDataExplorerSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству **type** источника действия копирования необходимо задать значение **AzureDataExplorerSource** | Yes |
| query | Запрос только для чтения в [формате KQL](/azure/kusto/query/). Используйте пользовательский запрос KQL в качестве ссылки. | Yes |
| queryTimeout | Время ожидания до истечения срока запроса. Значение по умолчанию — 10 мин. (00:10:00); допустимое максимальное значение — 1 час (01:00:00). | Нет  |

>[!NOTE]
>Размер источника в обозреватель данных Azure по умолчанию ограничен 500 000 записей или 64 МБ. Чтобы получить все записи без усечения, можно указать `set notruncation;` в начале запроса. Ссылаться на [запроса ограничения](https://docs.microsoft.com/azure/kusto/concepts/querylimits) на дополнительные сведения см.

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

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойству **type** приемника действия копирования необходимо присвоить значение **AzureDataExplorerSink** | Yes |
| ingestionMappingName | Имя предварительно созданной **[сопоставление](/azure/kusto/management/mappings#csv-mapping)** в таблице Kusto. Чтобы сопоставить столбцы из источника в обозреватель данных Azure — которая применяется к **[все поддерживаемые исходные хранилища/форматы](copy-activity-overview.md#supported-data-stores-and-formats)** включая CSV или JSON и Avro форматирует и т.д., можно использовать действие копирования [столбца сопоставление](copy-activity-schema-and-type-mapping.md) (неявно по имени или явно в соответствии с настройками) и/или сопоставления обозреватель данных Azure. | Нет  |

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

## <a name="next-steps"></a>Дальнейшие действия

* В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.

* Дополнительные сведения о [копирование данных из фабрики данных Azure в Azure Data Explorer](/azure/data-explorer/data-factory-load-data).