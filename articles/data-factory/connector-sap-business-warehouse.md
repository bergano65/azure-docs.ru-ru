---
title: Копирование данных от SAP BW
description: Узнайте, как копировать данные из SAP Business Warehouse на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.openlocfilehash: 0c37d77ca73ddbe8b79351f90275a1d639757633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923738"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Копирование данных из SAP Business Warehouse с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Текущая версия](connector-sap-business-warehouse.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из SAP Business Warehouse (BW). Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!TIP]
>Чтобы узнать общую поддержку ADF по сценарию интеграции данных SAP, см. [интеграцию данных SAP с помощью whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсом и руководством.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем SAP Business Warehouse поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из SAP Business Warehouse можно скопировать в любое поддерживаемое хранилище данных приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель SAP Business Warehouse поддерживает:

- SAP Business Warehouse **версии 7.x**.
- Копирование данных из **InfoCubes и QueryCubes** (включая запросы BEx) с помощью запросов многомерных выражений.
- Копирование данных с помощью базовой проверки подлинности.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот соединитель SAP Business Warehouse, сделайте следующее:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите **библиотеку SAP NetWeaver** на компьютере среды выполнения интеграции. Библиотеку SAP Netweaver можно получить у администратора SAP или непосредственно на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Найдите **примечание к SAP № 1025361**, чтобы узнать адрес для скачивания самой последней версии. Убедитесь, что вы выбираете **64-разрядную** библиотеку SAP NetWeaver, которая соответствует вашей установке Интеграции Runtime. Установите все файлы, включенные в состав пакета SDK RFC для SAP NetWeaver, согласно примечанию к SAP. Библиотека SAP NetWeaver также включена в состав клиентских инструментов SAP.

>[!TIP]
>Чтобы устранить неполадки с подключением к SAP BW, проверьте следующее:
>- В папке %windir%\system32 хранятся все библиотеки зависимостей, извлеченные из пакета SDK для NetWeaver RFC. Как правило, это библиотеки icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll и sapnwrfc.dll.
>- На компьютере с локальной средой выполнения интеграции включены порты (как правило, 3300 и 3201), необходимые для подключения к серверу SAP.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP Business Warehouse.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP Business Warehouse (BW) поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SapBw** | Да |
| server | Имя сервера, на котором размещен экземпляр SAP Business Warehouse. | Да |
| systemNumber | Номер системы SAP Business Warehouse.<br/>Допустимые значения: двузначное десятичное число, представленное в виде строки. | Да |
| clientid | Идентификатор клиента в системе SAP Business Warehouse.<br/>Допустимые значения: трехзначное десятичное число, представленное в виде строки. | Да |
| userName | Имя пользователя, имеющего доступ к серверу SAP. | Да |
| password | Пароль для пользователя Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Примере:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. Этот раздел содержит список свойств, поддерживаемых набором данных SAP BW.

Чтобы скопировать данные из SAP BW, установите свойство типа набора данных на **SapBwCube.** Сейчас для набора данных SAP Business Warehouse типа RelationalTable не поддерживаются какие-либо свойства типа.

**Примере:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Если вы `RelationalTable` использовали набор набранных данных, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW в качестве источника

Для копирования данных из SAP BW в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено: **SapBwSource** | Да |
| query | Указывает запрос многомерных выражений для чтения данных из экземпляра SAP Business Warehouse. | Да |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Если вы `RelationalSource` использовали набранный источник, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="data-type-mapping-for-sap-bw"></a>Сопоставление типов для SAP BW

При копировании данных из SAP BW используются следующие сопоставления типов данных SAP BW с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SAP BW | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| ACCP | Int |
| CHAR | Строка |
| CLNT | Строка |
| CURR | Decimal |
| CUKY | Строка |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Строка |
| LCHR | Строка |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Строка |
| ЕДИНИЦА ИЗМЕРЕНИЯ | Строка |
| DATS | Строка |
| NUMC | Строка |
| TIMS | Строка |


## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
