---
title: Копирование данных из Dynamics AX с помощью фабрики данных Azure
description: Узнайте, как копировать данные из Dynamics AX на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере Фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 07edc284c29ca209ee20e5de390e8126993f4ce3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681016"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Копирование данных из Dynamics AX с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure копировать данные из источника Dynamics AX. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Dynamics AX поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Dynamics AX можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Dynamics AX поддерживает копирование данных из Dynamics AX с помощью **протокола OData** с **проверкой подлинности субъекта-службы**.

>[!TIP]
>Этот соединитель также можно использовать для копирования данных из **Dynamics 365 Finance and Operations**. Ознакомьтесь со сведениями о [поддержке OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) Dynamics 365 и [методе проверки подлинности](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication).

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Dynamics AX.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать проверку подлинности субъекта-службы, выполните следующие действия.

1. Чтобы зарегистрировать сущность приложения в Azure Active Directory (Azure AD), необходимо следовать указаниям из раздела [Регистрация приложения в клиенте Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Запишите следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Перейдите к Dynamics AX и предоставьте субъекту-службе правильное разрешение для доступа к Dynamics AX.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Dynamics AX поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства**type** необходимо задать значение **DynamicsAX**. |Да |
| URL-адрес | Конечная точка OData экземпляра Dynamics AX (или Dynamics 365 Finance and Operations). |Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| клиенте | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Да |
| aadResourceId | Укажите ресурс AAD, для которого запрашивается авторизация. Например, если Dynamics имеет URL-адрес `https://sampledynamics.sandbox.operations.dynamics.com/data/`, то обычно соответствующий ресурс AAD имеет адрес `https://sampledynamics.sandbox.operations.dynamics.com`. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете выбрать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

**Пример**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Свойства набора данных

Этот раздел содержит список свойств, поддерживаемых набором данных Dynamics AX.

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md). 

Чтобы скопировать данные из Dynamics, установите для свойства **type** набора данных значение **DynamicsAXResource**. Поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** набора данных должно быть со значением **DynamicsAXResource**. | Да |
| path | Путь к сущности OData Dynamics AX. | Да |

**Пример**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником данных Dynamics AX.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Использование Dynamics AX в качестве источника

Чтобы копировать данные из Dynamics AX, установите для типа **источника** в действии копирования значение **DynamicsAXSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно быть со значением **DynamicsAXSource**. | Да |
| запрос | Параметры запроса OData для фильтрации данных. Пример: `"?$select=Name,Description&$top=5"`.<br/><br/>**Примечание.** Соединитель копирует данные из объединенного URL-адреса: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Дополнительные сведения см. в статье о [компонентах URL-адреса OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Нет |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
