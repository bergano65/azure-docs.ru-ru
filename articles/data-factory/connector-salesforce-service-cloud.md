---
title: Копирование данных из и в облако службы Salesforce
description: Узнайте, как копировать данные из облака службы продаж в поддерживаемые хранилища данных раковины или из поддерживаемых хранилив исходных данных в Облако Службы Salesforce, используя активность копирования в конвейере фабрики данных.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416672"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Копирование данных из и в облако службы Salesforce с помощью Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье излагается, как использовать активность копирования на фабрике данных Azure для копирования данных из и в облако службы Salesforce. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот облачный разъем службы Salesforce поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из облака службы Salesforce в любой поддерживаемый хранилище данных раковины. Вы также можете скопировать данные из любого поддерживаемого хранилища исходных данных в облако службы Salesforce. Список хранилив данных, которые поддерживаются в качестве источников или поглотителей в потоке активности Copy, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В частности, этот облачный разъем службы Salesforce поддерживает:

- Выпуски Salesforce Developer, Professional, Enterprise и Unlimited.
- Копирование данных в рабочую среду, песочницу или личный домен Salesforce, а также из них.

Разъем Salesforce построен поверх API Salesforce REST/Bulk. По умолчанию разъем использует [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) для копирования данных из Salesforce и использует [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) для копирования данных в Salesforce. Вы также можете явно установить версию API, используемую для чтения/записи данных через [ `apiVersion` свойство](#linked-service-properties) в связанном сервисе.

## <a name="prerequisites"></a>Предварительные требования

В Salesforce требуется включить разрешение API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).

## <a name="salesforce-request-limits"></a>Ограничения запросов Salesforce

Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Обратите внимание на следующие моменты.

- Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки.
- В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа.

Кроме того, в обоих случаях вы можете получить сообщение об ошибке "REQUEST_LIMIT_EXCEEDED". Дополнительные сведения см. в разделе API request limits (Ограничения запросов API) руководства об [ограничениях для разработчика Salesforce](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Data Factory, специфичных для облачного разъема Службы Salesforce.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Salesforce поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type |Свойство типа должно быть установлено на **SalesforceServiceCloud.** |Да |
| environmentUrl | Укажите URL-адрес экземпляра облака службы Salesforce. <br> Значение по умолчанию — `"https://login.salesforce.com"`. <br> Чтобы скопировать данные из песочницы, укажите `"https://test.salesforce.com"`. <br> Чтобы скопировать данные из пользовательского домена, укажите URL-адрес, например `"https://[domain].my.salesforce.com"`. |нет |
| username |Укажите имя пользователя для учетной записи пользователя. |Да |
| password |Укажите пароль для учетной записи пользователя.<br/><br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| securityToken |Укажите маркер безопасности для учетной записи пользователя. <br/><br/>Общие сведения о маркере безопасности см. в статье [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API). Токен безопасности можно пропустить только в том случае, если вы добавите IP-адрес Integration Runtime в [список доверенных IP-адресов](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) в Salesforce. При использовании ИК Azure обратитесь к [IP-адресам Azure Integration Runtime.](azure-integration-runtime-ip-addresses.md)<br/><br/>Для получения инструкций о том, как получить и сбросить маркер безопасности, [см.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |нет |
| версия_API | Укажите для использования версию Salesforce REST/Bulk `48.0`API, например. По умолчанию разъем использует [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) для копирования данных из Salesforce и использует [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) для копирования данных в Salesforce. | нет |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | "Нет" для источника, "Да" для приемника, если связанная с источником служба не имеет среды выполнения интеграции |

>[!IMPORTANT]
>При копировании данных в облаке службы Salesforce время интеграции Azure по умолчанию не может использоваться для выполнения копии. Другими словами, если служба, связанная с исходным источником, не имеет указанного времени выполнения интеграции, явно [создайте Runtime интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) с местом рядом с экземпляром облака службы Salesforce. Связать облачный сервис Salesforce, как в следующем примере.

**Пример хранения данных в фабрике данных**

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

**Пример хранения учетных данных в хранилище ключей**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе приводится список свойств, поддерживаемых набором данных отдела облачных данных службы Salesforce.

Для копирования данных из и в облаке службы Salesforce поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на **SalesforceServiceCloudObject.**  | Да |
| objectApiName | Имя объекта Salesforce, из которого извлекаются данные. | "Нет" для источника, "Да" для приемника |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: имя API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Примере:**

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

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **RelationalTable**. | Да |
| tableName | Название таблицы в облаке службы Salesforce. | Нет (если свойство query указано в источнике действия) |

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником и раковиной Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Облако службы salesforce как тип источника

Для копирования данных из облака службы Salesforce в разделе **источника активности** копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на **SalesforceServiceCloudSource.** | Да |
| query |Используйте пользовательский запрос для чтения данных. Вы можете использовать запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm), или запрос SQL-92. Дополнительные советы см. в разделе [Советы по запросам](#query-tips). Если запрос не указан, все данные облачного объекта службы Salesforce, указанные в "objectApiName" в наборе данных, будут извлечены. | Нет (если в наборе данных задано свойство objectApiName) |
| readBehavior | Указывает, следует ли запрашивать существующие записи или все записи, включая удаленные. Если значение не задано, по умолчанию используется первое значение. <br>Допустимые значения: **query** (по умолчанию), **queryAll**.  | нет |

> [!IMPORTANT]
> Для любых настраиваемых объектов **имя API** должно содержать приставку "__c".

![Фабрика данных — подключение к Salesforce: список имен API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Примере:**

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

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Облако службы Salesforce как тип раковины

Для копирования данных в облако службы Salesforce в разделе **«Поглотитель активности** копирования» поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **SalesforceServiceCloudSink.** | Да |
| writeBehavior | Поведение операции при записи.<br/>Допустимые значения: **Insert** (Вставка) и **Upsert** (Вставка-обновление). | Нет (по умолчанию используется Insert) |
| externalIdFieldName | Имя поля для внешнего идентификатора при операции upsert. Указанное поле должно быть определено как "Внешнее поле идентификатора" в облачном объекте службы Salesforce. Оно не может иметь значения NULL в соответствующих входных данных. | "Да" для операции Upsert (Вставка-обновление) |
| writeBatchSize | Количество строк данных, записанных в облако службы Salesforce в каждой партии. | Нет (значение по умолчанию — 5,000) |
| ignoreNullValues | Указывает, следует ли игнорировать значения NULL из входных данных во время операции записи.<br/>Допустимые значения: **true** и **false**.<br>- **True**: при выполнении операции upsert или обновления (update) оставьте данные в целевом объекте без изменений. При выполнении операции вставки (insert) вставьте определенное значение по умолчанию.<br/>- **False**: при выполнении операции upsert или обновления (update) обновите данные в целевом объекте до значения NULL. При выполнении операции вставки (insert) вставьте значение NULL. | Нет (по умолчанию используется значение false) |

**Примере:**

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

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Извлечение данных из облачного отчета службы Salesforce

Вы можете получить данные из отчетов Salesforce Service `{call "<report name>"}`Cloud, указав запрос как. Например, `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Извлечение удаленных записей из облачного рециркуляции службы Salesforce

Чтобы задать запрос на удаленные удаленные записи из облачного рециркуляции службы Salesforce, можно указать `readBehavior` как. `queryAll` 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Различия между синтаксисом запросов SOQL и SQL

При копировании данных из облака службы Salesforce можно использовать запрос SO'L или запрос S'L. Обратите внимание, что эти два запроса имеют различную поддержку синтаксиса и функциональности, их не следует смешивать. Предлагается использовать запрос SO'L, который используется нативной поддержкой сервиса Salesforce Cloud. Главные различия показаны в следующей таблице.

| Синтаксис | Режим SOQL | Режим SQL |
|:--- |:--- |:--- |
| Выбор столбцов | Необходимо перечислить поля для копирования в запросе, например.`SELECT field1, filed2 FROM objectname` | `SELECT *` поддерживается в дополнении к выделенному фрагменту столбца. |
| Кавычки | Имена полей или объектов не заключаются в кавычки. | Имена полей или объектов заключаются в кавычки, например `SELECT "id" FROM "Account"` |
| Формат даты и времени |  Подробнее см. [здесь](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm), а примеры — в следующем разделе. | Подробнее см. [здесь](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017), а примеры — в следующем разделе. |
| Логические значения | Представленные в виде `False` и `True`, например `SELECT … WHERE IsDeleted=True`. | Представленные в значении 0 или 1, например `SELECT … WHERE IsDeleted=1`. |
| Переименование столбцов | Не поддерживается. | Поддерживается, например, `SELECT a AS b FROM …`. |
| Связь | Поддерживается, например, `Account_vod__r.nvs_Country__c`. | Не поддерживается. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Извлечение данных с использованием предложения where для столбца даты и времени

При указании запроса SOQL или SQL обратите внимание на различие в формате даты и времени. Пример:

* **Образец СОЗЛ**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Образец СЗЛ**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Ошибка MALFORMED_QUERY:Truncated

Если вы нажмете на ошибку "MALFORMED_QUERY: Truncated", обычно это связано с тем, что у вас есть столбец типа JunctionIdList в данных, и Salesforce имеет ограничение на поддержку таких данных большим количеством строк. Чтобы смягчить последствия, попробуйте исключить столбец JunctionIdList или ограничить количество строк для копирования (можно перегородку на несколько запусков действий копирования).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Отображение типа данных для облака службы Salesforce

При копировании данных из облака службы Salesforce используются следующие отображения от типов данных Salesforce Service Cloud и временных типов данных Data Factory. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных службы Salesforce Cloud | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| Автонумерация |Строка |
| Флажок |Логическое |
| Валюта |Decimal |
| Дата |Дата и время |
| Дата и время |Дата и время |
| Email |Строка |
| Идентификатор |Строка |
| Связь для подстановки |Строка |
| Список множественного выбора |Строка |
| Number |Decimal |
| Процент |Decimal |
| Номер телефона |Строка |
| Список выбора |Строка |
| текст |Строка |
| Текстовое поле |Строка |
| Текстовое поле (длинное) |Строка |
| Текстовое поле (расширенное) |Строка |
| Текст (зашифрованный) |Строка |
| URL-адрес |Строка |

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
