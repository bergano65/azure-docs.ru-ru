---
title: Копирование данных из источника REST с помощью Фабрики данных Azure | Документация Майкрософт
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
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: ee47f464c59bd9deed98671f19cfcc6d2c3c1b39
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762486"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Копирование данных из конечной точки REST с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure копировать данные из конечной точки REST. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

Далее приводятся различия между соединителем REST, [соединителем HTTP](connector-http.md) и [соединителем веб-таблиц](connector-web-table.md).

- **Соединитель REST** предназначен для поддержки копирования данных из RESTful API. 
- **Соединитель HTTP** применяется для извлечения данных из любой конечной точки HTTP, например для скачивания файла. Пока этот соединитель REST недоступен, для копирования данных из RESTful API можно использовать соединитель HTTP, который поддерживается, но является менее функциональным по сравнению с соединителем REST.
- **Соединитель веб-таблиц** извлекает содержимое таблицы со страницы HTML.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из источника REST можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот универсальный соединитель REST, в частности, поддерживает следующее.

- Извлечение данных из конечной точки REST с помощью метода **GET** или **POST**.
- Получение данных с использованием одного из следующих типов аутентификации: **Анонимная**, **Базовая**, **Субъект-служба AAD** и **Управляемые удостоверения для ресурсов Azure**.
- **[Разбиение на страницы](#pagination-support)** в интерфейсах REST API.
- Копирование ответа JSON REST [как есть](#export-json-response-as-is) или его анализ с помощью [сопоставления схем](copy-activity-schema-and-type-mapping.md#schema-mapping). Поддерживаются только полезные данные ответа в **JSON**.

> [!TIP]
> Чтобы проверить запрос для извлечения данных, прежде чем настраивать соединитель REST в Фабрике данных, ознакомьтесь с требованиями спецификации API в отношении заголовка и текста. Для проверки можно использовать такие средства, как Postman или веб-браузер.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю REST.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы REST поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | **Тип** свойству должно быть присвоено **RestService**. | Yes |
| URL-адрес | Базовый URL-адрес службы REST. | Yes |
| enableServerCertificateValidation | Следует ли проверять SSL-сертификат на стороне сервера при подключении к конечной точке. | Нет <br /> (значение по умолчанию **true**) |
| authenticationType | Тип проверки подлинности, используемый для подключения к службе REST. Допустимые значения: **Anonymous**, **Basic**, **AadServicePrincipal** и **ManagedServiceIdentity**. Обратитесь к соответствующим разделам ниже, в которых описываются дополнительные свойства и примеры. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать Azure Integration Runtime или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, это свойство по умолчанию использует интегрированную среду выполнения Azure. |Нет  |

### <a name="use-basic-authentication"></a>Использование обычной проверки подлинности

Задайте для свойства **authenticationType** значение **Basic**. В дополнение к общим свойствам, описанных в предыдущих разделах, укажите следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
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

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| servicePrincipalId | Укажите идентификатор клиента приложения Azure Active Directory. | Yes |
| servicePrincipalKey | Укажите ключ приложения Azure Active Directory. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes |
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

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
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

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства **type** набора данных необходимо задать значение **RestResource**. | Yes |
| relativeUrl | Относительный URL-адрес ресурса, который содержит данные. Если свойство не задано, используется только URL-адрес, указанный в определении связанной службы. | Нет  |
| requestMethod | Метод HTTP. Допустимые значения: **GET** (по умолчанию) и **POST**. | Нет  |
| additionalHeaders | Дополнительные заголовки HTTP-запроса. | Нет  |
| requestBody | Текст HTTP-запроса. | Нет  |
| paginationRules | Правила разбиения на страницы для составления следующих запросов страниц. Дополнительные сведения см. в разделе, посвященном [поддержке разбиения на страницы](#pagination-support). | Нет  |

**Пример 1. Использование метода Get с разбиением на страницы**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Пример 2. Использование метода Post**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником REST.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST в качестве источника

В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство **type** источника действия копирования должно иметь значение **RestSource**. | Yes |
| httpRequestTimeout | Время ожидания (значение **Временной диапазон**) ответа для HTTP-запроса. Это значение является интервалом времени для получения ответа, а не считывания данных ответа. По умолчанию используется значение **00:01:40**.  | Нет  |
| requestInterval | Время ожидания перед отправкой запроса следующей страницы. Значение по умолчанию — **00:00:01** |  Нет  |

**Пример**

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

Как правило, REST API ограничивает размер полезных данных ответа на один запрос разумным числом, а для возвращения большого объема данных он разделяет результат на несколько страниц и требует от вызывающего объекта отправки последовательных запросов для получения следующей страницы результатов. Обычно запрос одной страницы является динамическим и состоит из сведений, возвращенных из ответа предыдущей страницы.

Этот универсальный соединитель REST поддерживает следующие шаблоны разбиения на страницы: 

* Следующий запрос абсолютный или относительный URL-адрес = значение свойства в текущий текст ответа
* Следующий запрос абсолютный или относительный URL-адрес = значение заголовка в текущем заголовки ответа
* параметр запроса следующего запроса = значение свойства в текущем тексте ответа;
* параметр запроса следующего запроса = значение заголовка в текущих заголовках ответа;
* заголовок следующего запроса = значение свойства в текущем тексте ответа;
* заголовок следующего запроса = значение заголовка в текущих заголовках ответа.

**Правила разбиения на страницы** определяются как словарь в наборе данных, который содержит одну пару "ключ — значение" или несколько с учетом регистра. Эта конфигурация будет использоваться для создания запроса, начиная со второй страницы. Соединитель будет прекращать итерацию при получении кода состояния HTTP 204 (Нет содержимого) или при возвращении значения null любым из выражений JSONPath в paginationRules.

**Поддерживаемые ключи** в правилах разбиения на страницы

| Ключ | ОПИСАНИЕ |
|:--- |:--- |
| AbsoluteUrl | Указывает URL-адрес для выполнения следующего запроса. Это может быть **относительный URL-адрес или абсолютный URL-адрес**. |
| QueryParameters.*параметр_запроса_запроса* ИЛИ QueryParameters['параметр_запроса_запроса'] | "параметр_запроса_запроса" — определяется пользователем и ссылается на одно имя параметра запроса в URL-адресе следующего запроса HTTP. |
| Headers.*заголовок_запроса* ИЛИ Headers['заголовок_запроса'] | "заголовок_запроса" — определяется пользователем и ссылается на одно имя заголовка в следующем запросе HTTP. |

**Поддерживаемые значения** в правилах разбиения на страницы

| Значение | ОПИСАНИЕ |
|:--- |:--- |
| Headers.*заголовок_ответа* ИЛИ Headers['заголовок_ответа'] | "заголовок_ответа" — определяется пользователем и ссылается на одно имя заголовка в текущем ответе HTTP, значение которого будет использоваться для выдачи следующего запроса. |
| Выражение JSONPath, начинающееся с $ (представляет корневую часть текста ответа) | Текст ответа должен содержать только один объект JSON. Выражение JSONPath должно возвращать одно значение-примитив, которое будет использоваться для выдачи следующего запроса. |

**Пример.**

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

Соответствующая конфигурация набора данных REST, в частности `paginationRules`, выглядит следующим образом.

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Экспорт ответа JSON "как есть"

Вы можете использовать этот соединитель REST для экспорта ответа JSON REST API "как есть" в различные файловые хранилища. Для такого независимого от схемы копирования пропустите раздел "структура" (также называемый *схема*) в наборе данных и сопоставление схемы в действии копирования.

## <a name="schema-mapping"></a>Сопоставление схем

Инструкции по копированию данных из конечной точки REST в табличный приемник см. в разделе о [сопоставлении схем](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Дальнейшие действия

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
