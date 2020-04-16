---
title: Копирование данных от SAP ECC
description: Узнайте, как копировать данные из SAP ECC в поддерживаемые хранилища данных, используемые в качестве приемника, с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413795"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Копирование данных от SAP ECC с помощью Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье излагается, как использовать активность копирования на фабрике данных Azure для копирования данных из центрального компонента SAP Enterprise (ECC). Для получения дополнительной информации смотрите [обзор активности Copy](copy-activity-overview.md).

>[!TIP]
>Чтобы узнать общую поддержку ADF по сценарию интеграции данных SAP, см. [интеграцию данных SAP с помощью whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсом и руководством.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем SAP ECC поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из SAP ECC можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилив данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В частности, этот соединитель SAP ECC поддерживает:

- Копирование данных от SAP ECC по версии SAP NetWeaver 7.0 и позже.
- Копирование данных с любых объектов, подвергающихся воздействию служб SAP ECC OData, таких как:

  - Таблицы или представления SAP.
  - Бизнес-приложение Программирование Интерфейс "BAPI" объектов.
  - Экстракторы данных.
  - Данные или промежуточные документы (IDOCs), отправленные в Интеграцию процессов SAP (PI), которые могут быть получены как OData через относительные адаптеры.

- Копирование данных с помощью базовой аутентификации.

>[!TIP]
>Чтобы скопировать данные из SAP ECC через таблицу или представление SAP, используйте разъем [таблицы SAP,](connector-sap-table.md) который быстрее и масштабируемее.

## <a name="prerequisites"></a>Предварительные требования

Как правило, SAP ECC предоставляет сущности через службы OData посредством шлюза SAP. Чтобы использовать этот соединитель SAP ECC, сделайте следующее:

- **Настройте шлюз SAP**. Для серверов с версиями SAP NetWeaver позже 7.4, SAP Gateway уже установлен. Для более ранних версий необходимо установить встроенный шлюз SAP или концентратор SAP Gateway, прежде чем экспонировать данные SAP ECC через службы OData. Чтобы настроить SAP Gateway, [installation guide](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)см.

- **Активируйте и настраивайте службу SAP OData.** Вы можете активировать службу OData через TCODE SICF в считанные секунды. Можно также настроить объекты, которые должны быть выставлены. Для получения дополнительной информации смотрите [пошагово-пошаговое руководство.](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей фабрики данных, специфичных для разъема SAP ECC.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы, связанной с SAP ECC:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| `type` | Для свойства `type` должно быть установлено значение `SapEcc`. | Да |
| `url` | URL-адрес службы SAP ECC OData. | Да |
| `username` | Имя пользователя используется для подключения к SAP ECC. | нет |
| `password` | Пароль простого текста, используемый для подключения к SAP ECC. | нет |
| `connectVia` | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если вы не указали время выполнения, используется время выполнения интеграции Azure по умолчанию. | нет |

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

Полный список разделов и свойств, доступных [Datasets](concepts-datasets-linked-services.md)для определения наборов данных, см. В следующем разделе приводится список свойств, поддерживаемых набором данных SAP ECC.

Чтобы скопировать данные из SAP ECC, установите свойство `type` набора данных. `SapEccResource`

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
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

Полный список разделов и свойств, доступных для определения [деятельности, см.](concepts-pipelines-activities.md) В следующем разделе приводится список свойств, поддерживаемых источником SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC как источник

Чтобы скопировать данные из SAP ECC, установите `type` свойство в `source` разделе действия копирования. `SapEccSource`

Следующие свойства поддерживаются в разделе `source` действия копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| `type` | Свойство `type` раздела копирования `source` должна быть `SapEccSource`установлена на . | Да |
| `query` | Параметры запроса OData для фильтрации данных. Пример:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Разъем SAP ECC копирует данные из объединенного URL::<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Дополнительные сведения см. в статье о [компонентах URL-адреса OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | нет |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Отображение типов данных для SAP ECC

При копировании данных из SAP ECC используются следующие отображения от типов данных OData для данных SAP ECC до промежуточных типов данных Azure Data Factory. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

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

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Дальнейшие действия

Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
