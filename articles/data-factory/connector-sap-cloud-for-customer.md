---
title: Копирование данных из SAP Cloud для клиента и обратно в него
description: Узнайте, как копировать данные SAP Cloud for Customer в поддерживаемые хранилища данных-приемники или из поддерживаемых исходных хранилищ данных в SAP Cloud for Customer с помощью фабрики данных.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 0b0352632491dbfc46ed4819653c6cb902eee4ce
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923722"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Копирование данных из SAP Cloud for Customer (C4C) с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в SAP Cloud for Customer (C4C) или из C4C. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!TIP]
>Сведения о общей поддержке ADF в сценарии интеграции данных SAP см. в статье [Интеграция данных SAP с помощью фабрики данных Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсион и рекомендациями.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Соединитель SAP для соединителя клиента поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные можно копировать из SAP Cloud for Customer в любой поддерживаемый приемник данных или из любого поддерживаемого источника данных в SAP Cloud for Customer. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель позволяет фабрике данных Azure копировать данные из SAP Cloud for Customer или в SAP Cloud for Customer, включая решения SAP Cloud for Sales, SAP Cloud for Service и SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP Cloud for Customer поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type нужно задать значение **SapCloudForCustomer**. | ДА |
| URL-адрес | URL-адрес службы SAP C4C OData. | ДА |
| Имя пользователя | Укажите имя пользователя для подключения к SAP C4C. | ДА |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | ДА |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника |

>[!IMPORTANT]
>Чтобы копировать данные в SAP Cloud for Customer, [создайте Azure IR](create-azure-integration-runtime.md#create-azure-ir) явным образом в расположении, близком к вашей системе SAP Cloud for Customer, и сопоставьте его в связанной службе, как показано в следующем примере:

**Пример.**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных SAP Cloud for Customer.

Чтобы скопировать данные из SAP Cloud for Customer, задайте для свойства type набора данных значение **SapCloudForCustomerResource**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **SapCloudForCustomerResource**. |ДА |
| path | Укажите путь к сущности SAP C4C OData. |ДА |

**Пример.**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C в качестве источника

Чтобы скопировать данные из SAP Cloud for Customer, задайте тип источника в действии копирования как **SapCloudForCustomerSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type нужно задать значение **SapCloudForCustomerSource**.  | ДА |
| query | Укажите пользовательский запрос OData для чтения данных. | Нет |

Образец запроса для получения данных за определенный день: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C в качестве приемника

Чтобы скопировать данные в SAP Cloud for Customer, задайте тип приемника в действии копирования как **SapCloudForCustomerSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type нужно задать значение **SapCloudForCustomerSink**.  | ДА |
| writeBehavior | Поведение операции при записи. Может иметь значение "Insert", "Update". | Нет. По умолчанию "Insert". |
| writeBatchSize | Размер пакета операции записи. Размер пакета для обеспечения максимальной производительности может различаться для разных таблиц или серверов. | Нет. По умолчанию 10. |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Сопоставление типов данных для SAP Cloud for Customer

При копировании данных из SAP Cloud for Customer используются следующие сопоставления типов данных SAP Cloud for Customer с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных OData SAP C4C | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Дата и время |
| Edm.Decimal | Decimal |
| Edm.Double | DOUBLE |
| Edm.Single | Отдельная |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Строка |
| Edm.Time | Интервал времени |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
