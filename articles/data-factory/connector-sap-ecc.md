---
title: Копирование данных из SAP ECC с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из SAP ECC в поддерживаемые хранилища данных, используемые в качестве приемника, с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827768"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Копирование данных из SAP ECC с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из SAP Enterprise Central Component (ECC). Дополнительные сведения см. в разделе [об обзоре действия копирования](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из SAP ECC можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP ECC поддерживает:

- Копирование данных из SAP ECC в SAP NetWeaver 7.0 и более поздних версий.
- Копирование данных из любые объекты, предоставляемые службами SAP ECC OData, такие как:

  - SAP таблиц или представлений.
  - Бизнес-объектов интерфейс прикладного программирования [BAPI].
  - Средства извлечения данных.
  - Данные или промежуточных документы (IDOC), отправленные для SAP процесс интеграции (PI), которые могут быть получены как OData через относительные адаптеры.

- Копирование данных с помощью обычной проверки подлинности.

>[!TIP]
>Чтобы скопировать данные из SAP ECC с помощью SAP таблицы или представления, используйте [таблицы SAP](connector-sap-table.md) соединитель, который является ускорение и большую масштабируемость.

## <a name="prerequisites"></a>предварительные требования

Как правило, SAP ECC предоставляет сущности через службы OData посредством шлюза SAP. Чтобы использовать этот соединитель SAP ECC, сделайте следующее:

- **Настройте шлюз SAP**. Для серверов с SAP NetWeaver версии позже, чем 7.4 шлюз SAP уже установлен. Для более ранних версий прежде чем предоставить данные SAP ECC через службы OData необходимо установить embedded шлюза SAP или система центра шлюза SAP. Чтобы настроить шлюз SAP, см. в разделе [руководство по установке](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Активировать и настроить службу SAP OData**. Вы можете активировать службы OData через TCODE SICF в секундах. Можно также настроить, какие объекты должны быть доступны. Дополнительные сведения см. в разделе [Пошаговое руководство](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP ECC.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP ECC поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | `type` Свойству должно быть присвоено `SapEcc`. | Да |
| `url` | URL-адрес службы SAP ECC OData. | Да |
| `username` | Имя пользователя, используемое для подключения к SAP ECC. | Нет |
| `password` | Текстовый пароль, используемый для подключения к SAP ECC. | Нет |
| `connectVia` | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Можно использовать локальную среду выполнения интеграции или среды выполнения интеграции Azure (если хранилище данных является общедоступным). Если вы не укажете время выполнения, `connectVia` использует среда выполнения интеграции Azure по умолчанию. | Нет |

### <a name="example"></a>Пример

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в разделе [наборы данных](concepts-datasets-linked-services.md). Следующий раздел содержит список свойств, поддерживаемых набором данных SAP ECC.

Чтобы скопировать данные из SAP ECC, задайте `type` свойства набора данных значение `SapEccResource`.

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `path` | Путь к сущности SAP ECC OData. | Да |

### <a name="example"></a>Пример

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в разделе [конвейеры](concepts-pipelines-activities.md). Следующий раздел содержит список свойств, поддерживаемых источником SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC в качестве источника

Чтобы скопировать данные из SAP ECC, задайте `type` свойство в `source` раздел для действия копирования `SapEccSource`.

Следующие свойства поддерживаются в действии копирования `source` раздел:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| `type` | `type` Свойства действия копирования `source` раздела должно быть присвоено `SapEccSource`. | Да |
| `query` | Параметры запроса OData для фильтрации данных. Пример:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Соединитель SAP ECC копирует данные из комбинированного URL-адреса:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Дополнительные сведения см. в статье о [компонентах URL-адреса OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Нет |

### <a name="example"></a>Пример

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Сопоставления типов данных для SAP ECC

При копировании данных из SAP ECC используются следующие сопоставления типов данных OData для данных SAP ECC для промежуточных типов данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных OData | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Сложные типы данных сейчас не поддерживаются.

## <a name="next-steps"></a>Следующие шаги

Список хранилищ данных, поддерживаются в качестве источников и приемников с помощью действия копирования в фабрике данных Azure, см. в разделе [поддерживаемых хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats).
