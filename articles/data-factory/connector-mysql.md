---
title: Копирование данных из MySQL с помощью фабрики данных Azure
description: Дополнительные сведения о соединителе MySQL в фабрике данных Azure, который позволяет скопировать данные из базы данных MySQL в хранилище данных, поддерживаемое в качестве приемника.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c03a7cb28820ef8aca4e10314a424ea068cb3cee
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930074"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Копирование данных из MySQL с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-onprem-mysql-connector.md)
> * [Текущая версия](connector-mysql.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных MySQL. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!NOTE]
>Чтобы скопировать данные из службы [базы данных Azure для MySQL](../mysql/overview.md) или в нее, используйте специализированный [соединитель базы данных Azure для MySQL](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель MySQL поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из базы данных MySQL можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель MySQL поддерживает MySQL **версии 5.6 и 5.7**.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Начиная с версии 3.7 служба Integration Runtime предоставляет встроенный драйвер MySQL, поэтому вам не потребуется устанавливать его вручную.

Для Integration Runtime (Self-hosted) версии ниже, чем 3.7, установите [соединитель MySQL Connector/Net для Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) версии 6.6.5–6.10.7 на компьютер со средой выполнения интеграции. Этот 32-разрядный драйвер совместим с 64-разрядным IR.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю MySQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы MySQL поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **MySql** | ДА |
| connectionString | Укажите сведения, необходимые для подключения к экземпляру базы данных Azure для MySQL.<br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `password` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | ДА |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | Описание | Параметры | Обязательно для заполнения |
|:--- |:--- |:--- |:--- |
| SSLMode | Этот параметр указывает, использует ли драйвер SSL-шифрование и проверку при подключении к MySQL. Например, `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(по умолчанию)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Нет |
| useSystemTrustStore | Этот параметр указывает, следует ли использовать сертификат ЦС из доверенного системного хранилища или из указанного PEM-файла. Например, `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(по умолчанию)** | Нет |

**Пример.**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
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

Ранее используемая связанная служба MySQL со приведенными ниже полезными данными по-прежнему поддерживается. Но мы рекомендуем в дальнейшем использовать более новую версию.

**Предыдущие полезные данные:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных MySQL.

Для копирования данных из MySQL поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type набора данных должно иметь значение **мисклтабле** . | ДА |
| tableName | Имя таблицы в базе данных MySQL. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Если вы использовали `RelationalTable` типизированный набор данных, он по-прежнему поддерживается как есть, хотя вы предлагаете использовать новый.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником MySQL.

### <a name="mysql-as-source"></a>MySQL в качестве источника

Чтобы скопировать данные из MySQL, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type источника действия копирования должно иметь значение **мисклсаурце** . | ДА |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Если вы использовали `RelationalSource` типизированный источник, он по-прежнему поддерживается как есть, хотя вы предлагаете использовать новый.

## <a name="data-type-mapping-for-mysql"></a>Сопоставление типов данных для MySQL

При копировании данных из MySQL для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных MySQL. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных MySQL | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
