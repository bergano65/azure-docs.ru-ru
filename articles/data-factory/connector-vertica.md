---
title: Копирование данных из Vertica с помощью Фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Vertica в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 857cae886dec2872c083771e4b1fd57a27d2991b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60935625"
---
# <a name="copy-data-from-vertica-using-azure-data-factory"></a>Копирование данных из Vertica с помощью Фабрики данных Azure 

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Vertica. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Vertica можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Vertica.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Vertica поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Vertica** | Да |
| connectionString | Строка для подключения к Vertica через интерфейс ODBC.<br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `pwd` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

**Пример.**

```json
{
    "name": "VerticaLinkedService",
    "properties": {
        "type": "Vertica",
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
    "name": "VerticaLinkedService",
    "properties": {
        "type": "Vertica",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Vertica.

Чтобы скопировать данные из Vertica, установите свойство type набора данных **VerticaTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать значение **VerticaTable** | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "VerticaDataset",
    "properties": {
        "type": "VerticaTable",
        "linkedServiceName": {
            "referenceName": "<Vertica linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых Vertica как источником.

### <a name="vertica-as-source"></a>Vertica в качестве источника

Чтобы копировать данные из Vertica, установите тип источника **VerticaSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type источника действия копирования необходимо задать значение **VerticaSource** | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromVertica",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Vertica input dataset name>",
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
                "type": "VerticaSource",
                "query": "SELECT * FROM MyTable"
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
