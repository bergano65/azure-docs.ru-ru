---
title: Копирование данных из службы Salesforce в облако и обратно с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из облака службы Salesforce в поддерживаемые хранилища данных-приемники или из поддерживаемых исходных хранилищ данных в облако службы SalesForce с помощью действия копирования в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: ac9b12f07a27b3bb8ff66d8a5637cb656e06abc6
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010565"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Копирование данных из службы Salesforce в облако и обратно с помощью фабрики данных Azure

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные в облако службы Salesforce и обратно. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель облачных служб Salesforce поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из облака службы Salesforce можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Вы также можете копировать данные из любого поддерживаемого исходного хранилища данных в облако службы Salesforce. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель облака службы Salesforce поддерживает:

- Выпуски Salesforce Developer, Professional, Enterprise и Unlimited.
- Копирование данных в рабочую среду, песочницу или личный домен Salesforce, а также из них.

Облачный соединитель службы Salesforce создан на основе API-интерфейса SalesForce RESTFUL и групповой обработки с [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) для копирования данных из и [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) для копирования данных в.

## <a name="prerequisites"></a>Предварительные требования

В Salesforce требуется включить разрешение API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).

## <a name="salesforce-request-limits"></a>Ограничения запросов Salesforce

Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Обратите внимание на следующие моменты.

- Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки.
- В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа.

Кроме того, в обоих случаях вы можете получить сообщение об ошибке "REQUEST_LIMIT_EXCEEDED". Дополнительные сведения см. в разделе API request limits (Ограничения запросов API) руководства об [ограничениях для разработчика Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю облака службы Salesforce.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Salesforce поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type |Для свойства Type необходимо задать значение **салесфорцесервицеклауд**. |Да |
| environmentUrl | Укажите URL-адрес облачного экземпляра службы Salesforce. <br> Значение по умолчанию — `"https://login.salesforce.com"`. <br> Чтобы скопировать данные из песочницы, укажите `"https://test.salesforce.com"`. <br> Чтобы скопировать данные из пользовательского домена, укажите URL-адрес, например `"https://[domain].my.salesforce.com"`. |Нет |
| username |Укажите имя пользователя для учетной записи пользователя. |Да |
| password |Укажите пароль для учетной записи пользователя.<br/><br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| securityToken |Укажите маркер безопасности для учетной записи пользователя. Инструкции по получению и сбросу маркера безопасности см. в [этом разделе](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Общие сведения о маркере безопасности см. в статье [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API).<br/><br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!IMPORTANT]
>При копировании данных в облако службы Salesforce Azure Integration Runtime по умолчанию нельзя использовать для выполнения копирования. Иными словами, если у связанной службы источника нет указанной среды выполнения интеграции, явно [создайте Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) с расположением рядом с экземпляром облачной службы Salesforce. Свяжите связанную облачную службу Salesforce Service, как показано в следующем примере.

**Пример. Хранение учетных данных в Фабрике данных**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример. Хранение учетных данных в Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых облачным набором данных службы Salesforce.

Для копирования данных из службы Salesforce в облако и обратно поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **салесфорцесервицеклаудобжект**.  | Да |
| objectApiName | Имя объекта Salesforce, из которого извлекаются данные. | "Нет" для источника, "Да" для приемника |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Пример.**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **RelationalTable**. | Да |
| tableName | Имя таблицы в облаке службы Salesforce. | Нет (если свойство query указано в источнике действия) |

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником облачной службы Salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Облако службы Salesforce в качестве типа источника

Чтобы скопировать данные из облака службы Salesforce, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type источника действия копирования должно иметь значение **салесфорцесервицеклаудсаурце**. | Да |
| query |Используйте пользовательский запрос для чтения данных. Вы можете использовать запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm), или запрос SQL-92. Дополнительные советы см. в разделе [Советы по запросам](#query-tips). Если запрос не указан, будут получены все данные облачного объекта службы Salesforce, указанного в "Обжектапинаме" в наборе данных. | Нет (если в наборе данных задано свойство objectApiName) |
| readBehavior | Указывает, следует ли запрашивать существующие записи или все записи, включая удаленные. Если значение не задано, по умолчанию используется первое значение. <br>Допустимые значения: **query** (по умолчанию), **queryAll**.  | Нет |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: список имен API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Облако службы Salesforce в качестве типа приемника

Чтобы скопировать данные в облако службы Salesforce, в разделе **приемника** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type приемника действия копирования должно иметь значение **салесфорцесервицеклаудсинк**. | Да |
| writeBehavior | Поведение операции при записи.<br/>Допустимые значения: **Insert** (Вставка) и **Upsert** (Вставка-обновление). | Нет (по умолчанию используется Insert) |
| externalIdFieldName | Имя поля для внешнего идентификатора при операции upsert. Указанное поле должно быть определено как "поле внешнего идентификатора" в облачном объекте службы Salesforce. Оно не может иметь значения NULL в соответствующих входных данных. | "Да" для операции Upsert (Вставка-обновление) |
| writeBatchSize | Число строк данных, записываемых в облако службы Salesforce в каждом пакете. | Нет (значение по умолчанию — 5,000) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**. При выполнении операции upsert или обновления (update) оставьте данные в целевом объекте без изменений. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**. При выполнении операции upsert или обновления (update) обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Советы по запросам

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Получение данных из облачного отчета службы Salesforce

Вы можете получить данные из облачных отчетов службы Salesforce, указав запрос в `{call "<report name>"}`качестве. Например, `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Извлечение удаленных записей из корзины службы Salesforce в облачной корзине

Чтобы запросить обратимо удаленные записи из корзины службы Salesforce, можно указать `readBehavior` в качестве. `queryAll` 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Различия между синтаксисом запросов SOQL и SQL

При копировании данных из облака службы Salesforce можно использовать либо запрос SOQL, либо SQL Query. Обратите внимание, что эти два запроса имеют различную поддержку синтаксиса и функциональности, их не следует смешивать. Вы предлагаете использовать запрос SOQL, который изначально поддерживается в облаке службы Salesforce. Главные различия показаны в следующей таблице.

| Синтаксис | Режим SOQL | Режим SQL |
|:--- |:--- |:--- |
| Выбор столбцов | Необходимо перечислить поля для копирования в запросе, например`SELECT field1, filed2 FROM objectname` | `SELECT *` поддерживается в дополнении к выделенному фрагменту столбца. |
| Кавычки | Имена полей или объектов не заключаются в кавычки. | Имена полей или объектов заключаются в кавычки, например `SELECT "id" FROM "Account"` |
| Формат даты и времени |  Подробнее см. [здесь](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm), а примеры — в следующем разделе. | Подробнее см. [здесь](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017), а примеры — в следующем разделе. |
| Логические значения | Представленные в виде `False` и `True`, например `SELECT … WHERE IsDeleted=True`. | Представленные в значении 0 или 1, например `SELECT … WHERE IsDeleted=1`. |
| Переименование столбцов | Не поддерживается. | Поддерживается, например, `SELECT a AS b FROM …`. |
| Связь | Поддерживается, например, `Account_vod__r.nvs_Country__c`. | Не поддерживается. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Извлечение данных с использованием предложения where для столбца даты и времени

При указании запроса SOQL или SQL обратите внимание на различие в формате даты и времени. Пример:

* **Пример SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Пример SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Ошибка MALFORMED_QUERY: усечено

Если вы столкнулись с ошибкой "MALFORMED_QUERY: Усечено ", обычно это связано с тем, что у вас есть столбец типа Жунктионидлист в данных, и Salesforce имеет ограничение на поддержку таких данных с большим количеством строк. Чтобы устранить эту проблемы, попробуйте исключить столбец Жунктионидлист или ограничить число копируемых строк (можно разделить на несколько запусков действия копирования).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Сопоставление типов данных для облака службы Salesforce

При копировании данных из облака службы Salesforce из типов данных облака службы Salesforce в промежуточные типы данных фабрики данных используются следующие сопоставления. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Cloud службы Salesforce | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Auto Number |Строковое |
| Checkbox |логический |
| Currency |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Адрес эл. почты |Строковое |
| Id |Строковое |
| Lookup Relationship |Строковое |
| Multi-Select Picklist |Строковое |
| Number |Decimal |
| Percent |Decimal |
| Phone |Строковое |
| Picklist |Строковое |
| Текст |Строковое |
| Text Area |Строковое |
| Text Area (Long) |Строковое |
| Text Area (Rich) |Строковое |
| Text (Encrypted) |Строковое |
| URL |Строковое |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
