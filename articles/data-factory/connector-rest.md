---
title: Копирование данных из источника с помощью фабрики данных Azure
description: Узнайте, как копировать данные из облака или локального источника REST на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: c691281f1ff0cf88d1ba61af43fad8e7782924aa
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278512"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Копирование данных из конечной точки REST с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure копировать данные из конечной точки REST. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

Далее приводятся различия между соединителем REST, [соединителем HTTP](connector-http.md) и [соединителем веб-таблиц](connector-web-table.md).

- **Соединитель RESTful** специально поддерживает копирование данных из интерфейсов API RESTful. 
- **Соединитель HTTP** применяется для извлечения данных из любой конечной точки HTTP, например для скачивания файла. Пока этот соединитель REST недоступен, для копирования данных из RESTful API можно использовать соединитель HTTP, который поддерживается, но является менее функциональным по сравнению с соединителем REST.
- **Соединитель веб-таблиц** извлекает содержимое таблицы со страницы HTML.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из источника REST можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот универсальный соединитель REST, в частности, поддерживает следующее.

- Извлечение данных из конечной точки REST с помощью метода **GET** или **POST**.
- Извлечение данных с помощью одной из следующих проверок подлинности: **Анонимный**, **базовый**, **субъект-служба AAD**и **управляемые удостоверения для ресурсов Azure**.
- **[Разбиение на страницы](#pagination-support)** в интерфейсах REST API.
- Копирование ответа JSON REST [как есть](#export-json-response-as-is) или его анализ с помощью [сопоставления схем](copy-activity-schema-and-type-mapping.md#schema-mapping). Поддерживаются только полезные данные ответа в **JSON**.

> [!TIP]
> Чтобы проверить запрос для извлечения данных, прежде чем настраивать соединитель REST в Фабрике данных, ознакомьтесь с требованиями спецификации API в отношении заголовка и текста. Для проверки можно использовать такие средства, как Postman или веб-браузер.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю REST.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы REST поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Для свойства **Type** необходимо задать значение **рестсервице**. | Yes |
| url | Базовый URL-адрес службы REST. | Yes |
| enableServerCertificateValidation | Следует ли проверять SSL-сертификат на стороне сервера при подключении к конечной точке. | Нет<br /> (значение по умолчанию **true**) |
| authenticationType | Тип проверки подлинности, используемый для подключения к службе REST. Допустимые значения: **Anonymous**, **Basic**, **AadServicePrincipal** и **ManagedServiceIdentity**. Обратитесь к соответствующим разделам ниже, в которых описываются дополнительные свойства и примеры. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, это свойство по умолчанию использует интегрированную среду выполнения Azure. |Нет |

### <a name="use-basic-authentication"></a>Использование обычной проверки подлинности

Задайте для свойства **authenticationType** значение **Basic**. В дополнение к общим свойствам, описанных в предыдущих разделах, укажите следующие свойства:

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| userName | Имя пользователя для доступа к конечной точке REST. | Yes |
| password | Пароль для пользователя (значение **userName**). Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных. Вы можете также [указать секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Пример**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>Использование проверки подлинности субъекта-службы AAD

Задайте для свойства **authenticationType** значение **AadServicePrincipal**. В дополнение к общим свойствам, описанных в предыдущих разделах, укажите следующие свойства:

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| servicePrincipalId | Укажите идентификатор клиента приложения Azure Active Directory. | Yes |
| servicePrincipalKey | Укажите ключ приложения Azure Active Directory. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| клиенте | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes |
| aadResourceId | Укажите ресурс AAD, для которого запрашивается авторизация, например `https://management.core.windows.net`.| Yes |

**Пример**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Использование аутентификации управляемых удостоверений для ресурсов Azure

Задайте для свойства **authenticationType** значение **ManagedServiceIdentity**. В дополнение к общим свойствам, описанных в предыдущих разделах, укажите следующие свойства:

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| aadResourceId | Укажите ресурс AAD, для которого запрашивается авторизация, например `https://management.core.windows.net`.| Yes |

**Пример**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Этот раздел содержит список свойств, поддерживаемых набором данных REST. 

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md). 

Для копирования данных из REST поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Для свойства **type** набора данных необходимо задать значение **RestResource**. | Yes |
| relativeUrl | Относительный URL-адрес ресурса, который содержит данные. Если свойство не задано, используется только URL-адрес, указанный в определении связанной службы. Соединитель HTTP копирует данные из комбинированного URL-адреса: `[URL specified in linked service]/[relative URL specified in dataset]`. | Нет |

Если вы настраиваете `requestMethod`, `additionalHeaders`, `requestBody` и `paginationRules` в наборе данных, он по-прежнему поддерживается как есть, хотя вы предлагаете использовать новую модель в источнике действия в дальнейшем.

**Пример**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником REST.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST в качестве источника

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | обязательные |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно иметь значение **RestSource**. | Yes |
| requestMethod | Метод HTTP. Допустимые значения: **GET** (по умолчанию) и **POST**. | Нет |
| additionalHeaders | Дополнительные заголовки HTTP-запроса. | Нет |
| requestBody | Текст HTTP-запроса. | Нет |
| paginationRules | Правила разбиения на страницы для составления следующих запросов страниц. Дополнительные сведения см. в разделе, посвященном [поддержке разбиения на страницы](#pagination-support). | Нет |
| httpRequestTimeout | Время ожидания (значение **Временной диапазон**) ответа для HTTP-запроса. Это значение является интервалом времени для получения ответа, а не считывания данных ответа. По умолчанию используется значение **00:01:40**.  | Нет |
| requestInterval | Время ожидания перед отправкой запроса следующей страницы. Значение по умолчанию — **00:00:01** |  Нет |

>[!NOTE]
>Соединитель RESTFUL игнорирует любой заголовок Accept, указанный в `additionalHeaders`. Так как соединитель RESTFUL поддерживает только ответ в JSON, функция TT автоматически создаст заголовок `Accept: application/json`.

**Пример 1. использование метода Get с разбивкой на страницы**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Пример 2. Использование метода POST**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Поддержка разбиения на страницы

Обычно REST API ограничивает размер полезных данных ответа одного запроса по разумному числу; Хотя для получения большого объема данных он разделяет результат на несколько страниц и требует от вызывающих объектов отправку последовательных запросов для получения следующей страницы результата. Обычно запрос одной страницы является динамическим и состоит из сведений, возвращенных из ответа предыдущей страницы.

Этот универсальный соединитель REST поддерживает следующие шаблоны разбиения на страницы: 

* Абсолютный или относительный URL-адрес следующего запроса = значение свойства в текущем тексте ответа
* Абсолютный или относительный URL-адрес следующего запроса = значение заголовка в текущем заголовке ответа
* параметр запроса следующего запроса = значение свойства в текущем тексте ответа;
* параметр запроса следующего запроса = значение заголовка в текущих заголовках ответа;
* заголовок следующего запроса = значение свойства в текущем тексте ответа;
* заголовок следующего запроса = значение заголовка в текущих заголовках ответа.

**Правила разбиения на страницы** определяются в виде словаря в наборе данных, который содержит одну или несколько пар "ключ-значение", учитывающих регистр. Эта конфигурация будет использоваться для создания запроса, начиная со второй страницы. Соединитель перестанет проходить итерацию, когда он получает код состояния HTTP 204 (без содержимого), или любое из выражений JSONPath в "Пагинатионрулес" возвращает значение null.

**Поддерживаемые ключи** в правилах разбиения на страницы

| Ключ | ОПИСАНИЕ |
|:--- |:--- |
| AbsoluteUrl | Указывает URL-адрес для выполнения следующего запроса. Это может быть **абсолютный URL-адрес или относительный URL-адрес**. |
| QueryParameters.*параметр_запроса_запроса* ИЛИ QueryParameters['параметр_запроса_запроса'] | "параметр_запроса_запроса" — определяется пользователем и ссылается на одно имя параметра запроса в URL-адресе следующего запроса HTTP. |
| Headers.*заголовок_запроса* ИЛИ Headers['заголовок_запроса'] | "заголовок_запроса" — определяется пользователем и ссылается на одно имя заголовка в следующем запросе HTTP. |

**Поддерживаемые значения** в правилах разбиения на страницы

| Значение | ОПИСАНИЕ |
|:--- |:--- |
| Headers.*заголовок_ответа* ИЛИ Headers['заголовок_ответа'] | "заголовок_ответа" — определяется пользователем и ссылается на одно имя заголовка в текущем ответе HTTP, значение которого будет использоваться для выдачи следующего запроса. |
| Выражение JSONPath, начинающееся с $ (представляет корневую часть текста ответа) | Текст ответа должен содержать только один объект JSON. Выражение JSONPath должно возвращать одно значение-примитив, которое будет использоваться для выдачи следующего запроса. |

**Пример**

Facebook Graph API возвращает ответ со следующей структурой. В этом случае URL-адрес следующей странице представлен в ***paging.next***.

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Ниже приведена соответствующая конфигурация источника действия копирования, особенно `paginationRules`.

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>Экспорт ответа JSON "как есть"

Вы можете использовать этот соединитель REST для экспорта ответа JSON REST API "как есть" в различные файловые хранилища. Для такого независимого от схемы копирования пропустите раздел "структура" (также называемый *схема*) в наборе данных и сопоставление схемы в действии копирования.

## <a name="schema-mapping"></a>Сопоставление схем

Инструкции по копированию данных из конечной точки REST в табличный приемник см. в разделе о [сопоставлении схем](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Дополнительная информация

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
