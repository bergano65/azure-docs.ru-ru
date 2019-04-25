---
title: Копирование данных из PayPal с помощью фабрики данных Azure (предварительная версия) | Документы Майкрософт
description: Узнайте, как копировать данные из PayPal в поддерживаемые хранилища данных, используемые в качестве приемников, с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: dcc54b0f67b9bf08df602c3eb9a4bcb0ea699ee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405891"
---
# <a name="copy-data-from-paypal-using-azure-data-factory-preview"></a>Копирование данных из PayPal с помощью фабрики данных Azure (предварительная версия)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из PayPal. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!IMPORTANT]
> Сейчас этот соединитель доступен в режиме предварительной версии. Попробуйте поработать с ним и оставьте свой отзыв. Если вы хотите использовать в своем решении зависимость от соединителей в предварительной версии, обратитесь в службу [поддержки Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из PayPal в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю PayPal.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы PayPal поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать следующее значение: **PayPal** | Yes |
| host | URL-адрес экземпляра PayPal. (api.sandbox.paypal.com).  | Yes |
| clientid | Идентификатор клиента, связанный с приложением PayPal.  | Yes |
| clientSecret | Секрет клиента, связанный с приложением PayPal. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Указывает, шифруются ли конечные точки источника данных с помощью протокола HTTPS. По умолчанию используется значение true.  | Нет  |
| useHostVerification | Указывает, следует ли требовать, чтобы имя узла в сертификате сервера совпадало с именем узла сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |
| usePeerVerification | Указывает, следует ли проверять удостоверение сервера при подключении по протоколу SSL. По умолчанию используется значение true.  | Нет  |

**Пример.**

```json
{
    "name": "PayPalLinkedService",
    "properties": {
        "type": "PayPal",
        "typeProperties": {
            "host" : "api.sandbox.paypal.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных PayPal.

Чтобы скопировать данные из PayPal, задайте для свойства type набора данных значение **PayPalObject**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **PayPalObject** | Yes |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "PayPalDataset",
    "properties": {
        "type": "PayPalObject",
        "linkedServiceName": {
            "referenceName": "<PayPal linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником PayPal.

### <a name="paypal-as-source"></a>PayPal в качестве источника

Чтобы копировать данные из PayPal, задайте для типа источника в действии копирования значение **PayPalSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь следующее значение: **PayPalSource** | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM Payment_Experience"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromPayPal",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PayPal input dataset name>",
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
                "type": "PayPalSource",
                "query": "SELECT * FROM Payment_Experience"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
