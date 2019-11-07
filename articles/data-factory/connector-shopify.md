---
title: Копирование данных из Shopify с помощью фабрики данных Azure (предварительная версия)
description: Узнайте, как копировать данные из Shopify в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.openlocfilehash: 00d1550e53dccc04240a622d3aaa8634537e6802
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680179"
---
# <a name="copy-data-from-shopify-using-azure-data-factory-preview"></a>Копирование данных из Shopify с помощью фабрики данных Azure (предварительная версия)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Shopify. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!IMPORTANT]
> Сейчас этот соединитель доступен в режиме предварительной версии. Попробуйте поработать с ним и оставьте свой отзыв. Если вы хотите использовать в своем решении зависимость от соединителей в предварительной версии, обратитесь в службу [поддержки Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Shopify поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Shopify можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Shopify.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Shopify поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Shopify**. | Да |
| host | Конечная точка сервера Shopify (mystore.myshopify.com).  | Да |
| accessToken | Маркер доступа для API, который может использоваться для доступа к данным Shopify. Срок действия маркера не ограничен, если используется автономный режим. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| useEncryptedEndpoints | Указывает, шифруются ли конечные точки источника данных с помощью протокола HTTPS. По умолчанию используется значение true.  | Нет |
| useHostVerification | Указывает, следует ли требовать, чтобы имя узла в сертификате сервера совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет |
| usePeerVerification | Указывает, следует ли проверять удостоверение сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет |

**Пример**

```json
{
    "name": "ShopifyLinkedService",
    "properties": {
        "type": "Shopify",
        "typeProperties": {
            "host" : "mystore.myshopify.com",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Shopify.

Чтобы скопировать данные из Shopify, задайте для свойства type набора данных значение **ShopifyObject**. Поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **шопифйобжект** . | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "ShopifyDataset",
    "properties": {
        "type": "ShopifyObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Shopify linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Shopify.

### <a name="shopify-as-source"></a>Shopify в качестве источника

Чтобы скопировать данные из Shopify, задайте для типа источника в действии копирования значение **ShopifySource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **ShopifySource**. | Да |
| запрос | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM "Products" WHERE Product_Id = '123'"`. | Нет (если для набора данных задано свойство tableName) |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromShopify",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Shopify input dataset name>",
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
                "type": "ShopifySource",
                "query": "SELECT * FROM \"Products\" WHERE Product_Id = '123'"
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
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
