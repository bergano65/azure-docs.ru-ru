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
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 3f51ad3d0a75ab1f0662ea0b747852fcb98e5026
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259043"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Копирование данных из SAP ECC с помощью фабрики данных Azure

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные из SAP Enterprise Central (ECC). Дополнительные сведения см. в разделе [Общие сведения о действиях копирования](copy-activity-overview.md).

>[!TIP]
>Сведения о общей поддержке ADF в сценарии интеграции данных SAP см. в статье [Интеграция данных SAP с помощью фабрики данных Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсион и рекомендациями.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из SAP ECC можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP ECC поддерживает:

- Копирование данных из SAP ECC в SAP NetWeaver версии 7,0 и более поздних версиях.
- Копирование данных из любых объектов, предоставляемых службами SAP ECC OData, например:

  - Таблицы или представления SAP.
  - Объекты интерфейса программирования бизнес-приложений [BAPI].
  - Средства извлечения данных.
  - Данные или промежуточные документы (iDoc), отправляемые в службу SAP Process Integration (PI), которые могут быть получены в виде OData через относительные адаптеры.

- Копирование данных с использованием обычной проверки подлинности.

>[!TIP]
>Чтобы скопировать данные из SAP ECC через таблицу или представление SAP, используйте соединитель [таблиц SAP](connector-sap-table.md) , который является более быстрым и масштабируемым.

## <a name="prerequisites"></a>Предварительные требования

Как правило, SAP ECC предоставляет сущности через службы OData посредством шлюза SAP. Чтобы использовать этот соединитель SAP ECC, сделайте следующее:

- **Настройте шлюз SAP**. Для серверов с версиями SAP NetWeaver более поздней версии, чем 7,4, шлюз SAP уже установлен. Для более ранних версий необходимо установить встроенный шлюз SAP или систему концентратора шлюза SAP, прежде чем предоставлять данные SAP ECC через службы OData. Сведения о настройке шлюза SAP см. в разделе [руководства по установке](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Активируйте и настройте службу SAP OData**. Вы можете активировать службу OData через ТКОДЕ СИКФ за считаные секунды. Также можно настроить, какие объекты должны быть предоставлены. Дополнительные сведения см. в разделе Пошаговое [руководство](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP ECC.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP ECC поддерживаются следующие свойства:

| Свойство | Описание | Обязательное значение |
|:--- |:--- |:--- |
| `type` | `type` Для`SapEcc`свойства необходимо задать значение. | Да |
| `url` | URL-адрес службы SAP ECC OData. | Да |
| `username` | Имя пользователя, используемое для подключения к SAP ECC. | Нет |
| `password` | Пароль в формате обычного текста, используемый для подключения к SAP ECC. | Нет |
| `connectVia` | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указать среду выполнения, используется среда выполнения интеграции Azure по умолчанию. | Нет |

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в разделе [наборы данных](concepts-datasets-linked-services.md). В следующем разделе приведен список свойств, поддерживаемых набором данных SAP ECC.

Чтобы скопировать данные из SAP ECC, присвойте `type` свойству `SapEccResource`набора данных значение.

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательное значение |
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
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. в разделе [конвейеры](concepts-pipelines-activities.md). В следующем разделе приведен список свойств, поддерживаемых источником SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC в качестве источника

Чтобы скопировать данные из SAP ECC, задайте `type` для `SapEccSource`свойства `source` в разделе действия копирования значение.

В `source` разделе действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательное значение |
|:--- |:--- |:--- |
| `type` | Свойство раздела действия копирования должно иметь значение. `SapEccSource` `type` `source` | Да |
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

При копировании данных из SAP ECC в промежуточные типы данных фабрики данных Azure используются следующие сопоставления типов данных для SAP ECC. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

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
> Сложные типы данных в настоящее время не поддерживаются.

## <a name="next-steps"></a>Следующие шаги

Список хранилищ данных, поддерживаемых действием копирования в фабрике данных Azure в качестве источников и приемников, см. в разделе [Supported Data Stores (поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats)).
