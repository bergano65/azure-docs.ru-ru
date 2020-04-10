---
title: Копирование данных из Google BigQuery с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Google BigQuery в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных.
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
ms.date: 09/04/2019
ms.openlocfilehash: 9fe6b494398337dd49bd8f0fe53b24666412a1b0
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991592"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Копирование данных из Google BigQuery с помощью фабрики данных Azure

Из этой статьи вы узнаете, как с помощью действия копирования в фабрике данных Azure копировать данные из Google BigQuery. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Google Big'sry поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из Google BigQuery можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилив данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В фабрике данных предоставляется встроенный драйвер, который обеспечивает подключение. Поэтому не нужно вручную устанавливать драйвер для использования этого соединителя.

>[!NOTE]
>Этот соединитель Google BigQuery создан на основе API-интерфейсов BigQuery. Учтите, что в BigQuery ограничено максимальное число входящих запросов и применяются соответствующие квоты на каждый проект. Дополнительные сведения см. в разделе о [квотах и ограничениях на запросы API](https://cloud.google.com/bigquery/quotas#api_requests). Не активируйте слишком много одновременных запросов к учетной записи.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Google BigQuery.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google BigQuery поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **GoogleBigQuery**. | Да |
| project | Идентификатор проекта BigQuery по умолчанию для отправки запросов.  | Да |
| additionalProjects | Список разделенных запятыми идентификаторов общедоступных проектов BigQuery для доступа.  | нет |
| requestGoogleDriveScope | Определяет, следует ли подавать запрос на доступ к Google Drive. Если разрешить доступ к Google Drive, включится поддержка для федеративных таблиц, которые объединяют данные BigQuery с данными с Google Drive. По умолчанию используется значение **false**.  | нет |
| authenticationType | Механизм OAuth 2.0 для аутентификации. ServiceAuthentication может использоваться только в локальных средах выполнения интеграции. <br/>Допустимые значения: **UserAuthentication** и **ServiceAuthentication**. В разделах ниже описываются дополнительные свойства и приведены примеры JSON для поддерживаемых типов проверки подлинности. | Да |

### <a name="using-user-authentication"></a>Использование проверки подлинности пользователей

Задайте для свойства authenticationType значение **UserAuthentication** и укажите следующие свойства вместе с универсальными свойствами, описанными в предыдущем разделе:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| clientid | Идентификатор приложения, используемого для создания маркера обновления. | нет |
| clientSecret | Секрет приложения, используемого для создания маркера обновления. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| refreshtoken | Маркер обновления, полученный из Google, используемый для авторизации доступа к BigQuery. Сведения о том, как его получить, см. [здесь](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) и [здесь](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | нет |

**Примере:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Использование проверки подлинности службы

Задайте для свойства authenticationType значение **ServiceAuthentication** и укажите перечисленные ниже свойства вместе с универсальными свойствами, описанными в предыдущем разделе. Этот тип проверки подлинности можно использовать только в локальных средах выполнения интеграции.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| email | Идентификатор электронной почты учетной записи службы, используемый для ServiceAuthentication. Может использоваться только в локальной среде выполнения интеграции.  | нет |
| keyFilePath | Полный путь к файлу ключа P12, используемый для аутентификации адреса электронной почты учетной записи службы. | нет |
| trustedCertPath | Полный путь файла .pem, содержащего доверенные сертификаты CA, используемые для проверки сервера при подключении к TLS. Это свойство может быть установлено только при использовании TLS на самостоятельном запуске интеграции. Значением по умолчанию является файл cacerts.pem, который устанавливается вместе со средой выполнения интеграции.  | нет |
| useSystemTrustStore | Указывает, следует ли использовать сертификат ЦС из доверенного системного хранилища или из указанного PEM-файла. По умолчанию используется значение **false**.  | нет |

**Примере:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Google BigQuery.

Чтобы скопировать данные из Google BigQuery, установите для свойства type набора данных значение **GoogleBigQueryObject**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **GoogleBig's'ryObject** | Да |
| набор данных | Название набора данных Google Big'еry. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Имя таблицы. Это свойство поддерживается для обратной совместимости. Для новой рабочей `dataset` `table`нагрузки, использование и . | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых типом источника Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource в качестве типа источника

Чтобы копировать данные Google BigQuery, установите тип источника **GoogleBigQuerySource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type источника действия копирования необходимо задать значение **GoogleBigQuerySource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
