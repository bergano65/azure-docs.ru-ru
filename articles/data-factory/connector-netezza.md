---
title: Копирование данных из Netezza с помощью Фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Netezza в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: c3c179cfbf86c2dddfb34b46540aba8898038751
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966492"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Копирование данных из Netezza с помощью Фабрики данных Azure

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные из Netezza. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Netezza можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В Фабрике данных Azure предоставляется встроенный драйвер, который обеспечивает подключение. Не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

Конвейер можно создать с помощью действия копирования и пакета SDK для .NET, пакета SDK для Python, Azure PowerShell, REST API либо шаблона Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Netezza.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Netezza поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **Netezza**. | Да |
| connectionString | Строка для подключения к Netezza через интерфейс ODBC. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `pwd` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. В следующей таблице описаны дополнительные свойства, которые можно задать.

| Свойство | Описание | Обязательное значение |
|:--- |:--- |:--- |
| SecurityLevel | Уровень безопасности (SSL/TLS), который драйвер использует для подключения к хранилищу данных. Пример: `SecurityLevel=preferredSecured`. Поддерживаемые значения:<br/>- **Только незащищенное** (**onlyUnSecured**): Драйвер не использует SSL.<br/>- **Предпочтительно незащищенное (preferredUnSecured) (по умолчанию)** : Если сервер предоставляет выбор, драйвер не использует SSL. <br/>- **Предпочтительно защищенное (preferredSecured)** : Если сервер предоставляет выбор, драйвер использует SSL. <br/>- **Только защищенное (onlySecured)** : Драйвер не подключается, если SSL-подключение недоступно. | Нет |
| CaCertFile | Полный путь к SSL-сертификату, используемому сервером. Пример: `CaCertFile=<cert path>;`| Да, если протокол SSL включен |

**Пример**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: хранение пароля в Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Этот раздел содержит список свойств, поддерживаемых набором данных Netezza.

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md).

Чтобы скопировать данные из Netezza, установите для свойства **type** набора данных значение **NetezzaTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type набора данных должно иметь следующее значение: **NetezzaTable** | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником Netezza.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza в качестве источника

Чтобы копировать данные из Netezza, установите для типа **source** в действии копирования значение **NetezzaSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно иметь значение **NetezzaSource**. | Да |
| запрос | Используйте пользовательский SQL-запрос для чтения данных. Пример: `"SELECT * FROM MyTable"` | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Следующие шаги

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
