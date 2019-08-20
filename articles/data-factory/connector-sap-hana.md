---
title: Копирование данных из SAP HANA с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из SAP HANA на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: jingwang
ms.openlocfilehash: e9b024fc3c07670201cf72cf80c0b69bf68f1cc8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726001"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Копирование данных из SAP HANA с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-sap-hana-connector.md)
> * [Текущая версия](connector-sap-hana.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных SAP HANA. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из базы данных SAP HANA можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP HANA поддерживает:

- Копирование данных из всех версий базы данных SAP HANA.
- Копирование данных из **информационных моделей Hana** (например, представлений аналитики и вычислений) и **таблиц строк и столбцов**.
- копирование данных с использованием **базовой** проверки подлинности или проверки подлинности **Windows**.

> [!TIP]
> Чтобы скопировать данные **в** хранилище данных SAP HANA, используйте универсальный соединитель ODBC. Подробные сведения см. в разделе о [приемнике SAP HANA](connector-odbc.md#sap-hana-sink). Обратите внимание, что связанные службы для соединителя SAP HANA и соединитель ODBC нельзя использовать повторно, так как они имеют разные типы.

## <a name="prerequisites"></a>предварительные требования

Чтобы использовать этот соединитель SAP HANA, сделайте следующее:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите на компьютере среды выполнения интеграции драйвер ODBC SAP HANA. Драйвер ODBC для SAP HANA можно скачать на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Выполните поиск по ключевой фразе **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP HANA.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP HANA поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать значение **SapHana** | Да |
| connectionString | Укажите сведения, необходимые для подключения к SAP HANA с помощью **обычной проверки** подлинности или **проверки подлинности Windows**. Ознакомьтесь с приведенными ниже примерами.<br>В строке подключения параметр "сервер/порт" является обязательным (порт по умолчанию — 30015), а имя пользователя и пароль являются обязательными при использовании обычной проверки подлинности. Дополнительные дополнительные параметры см. в разделе [SAP HANA свойства подключения ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) .<br/>Можно также поместить пароль в Azure Key Vault и извлечь конфигурацию пароля из строки подключения. Дополнительные сведения см. в разделе [хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md) статье. | Да |
| userName | Укажите имя пользователя при использовании проверки подлинности Windows. Пример: `user@domain.com` | Нет |
| password | Укажите пароль для учетной записи пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Пример. использование обычной проверки подлинности**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример. Использование проверки подлинности Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Если вы использовали SAP HANA связанную службу со следующими полезными данными, она по-прежнему поддерживается "как есть", хотя вы предлагаете использовать новую пересылку.

**Пример.**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных SAP HANA.

Чтобы скопировать данные из SAP HANA, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **сафанатабле** | Да |
| schema | Имя схемы в базе данных SAP HANA. | Нет (если свойство query указано в источнике действия) |
| table | Имя таблицы в базе данных SAP HANA. | Нет (если свойство query указано в источнике действия) |

**Пример.**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Если вы использовали `RelationalTable` типизированный набор данных, он по-прежнему поддерживается "как есть", хотя вы можете использовать новый объект, который будет использоваться в дальнейшем.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA в качестве источника

Чтобы скопировать данные из SAP HANA, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type источника действия копирования необходимо задать значение **сафанасаурце** | Да |
| query | Указывает SQL-запрос для чтения данных из экземпляра SAP HANA. | Да |
| packetSize | Указывает размер сетевого пакета (в килобайтах) для разбиения данных на несколько блоков. При наличии большого объема данных для копирования увеличение размера пакета может увеличить скорость чтения с SAP HANA в большинстве случаев. При изменении размера пакета рекомендуется использовать тестирование производительности. | Нет.<br>Значение по умолчанию — 2048 (2 МБ). |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Если вы использовали `RelationalSource` типизированный источник копирования, он по-прежнему поддерживается "как есть", хотя вы можете использовать новый вариант.

## <a name="data-type-mapping-for-sap-hana"></a>Сопоставление типов данных для SAP HANA

При копировании данных из SAP HANA используются следующие сопоставления типов данных SAP HANA с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SAP HANA | Тип промежуточных данных фабрики данных |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| БИНТЕКСТ            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
