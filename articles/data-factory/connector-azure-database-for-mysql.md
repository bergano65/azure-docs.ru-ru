---
title: Копирование данных в базу данных Azure для MySQL и из нее
description: Узнайте, как копировать данные в базу данных Azure для MySQL и обратно с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: cd3a069eb24b6390629cfaf9a4f19f47e8978d8a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931647"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Копирование данных в базу данных Azure для MySQL и из нее с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Azure для MySQL. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

Этот соединитель является специализированным для [службы базы данных Azure для MySQL](../mysql/overview.md). Чтобы скопировать данные из универсальной базы данных MySQL, расположенной в локальной среде или в облаке, используйте [соединитель MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель базы данных Azure для MySQL поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из базы данных Azure для MySQL можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Вы также можете скопировать данные из любого поддерживаемого исходного хранилища данных в базу данных Azure для MySQL. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для соединителя базы данных Azure для MySQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных Azure для MySQL поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureMySql**. | ДА |
| connectionString | Укажите сведения, необходимые для подключения к экземпляру базы данных Azure для MySQL. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `password` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | ДА |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

Типичная строка подключения — `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | Описание | Параметры | Обязательно для заполнения |
|:--- |:--- |:--- |:--- |
| SSLMode | Этот параметр указывает, использует ли драйвер SSL-шифрование и проверку при подключении к MySQL. Например, `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(по умолчанию)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Нет |
| useSystemTrustStore | Этот параметр указывает, следует ли использовать сертификат ЦС из доверенного системного хранилища или из указанного PEM-файла. Например, `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(по умолчанию)** | Нет |

**Пример.**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
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
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных базы данных Azure для MySQL.

Чтобы скопировать данные из базы данных Azure для MySQL, задайте для свойства type набора данных значение **AzureMySqlTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство type набора данных должно иметь значение **AzureMySqlTable**. | ДА |
| tableName | Имя таблицы в базе данных MySQL. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником базы данных Azure для MySQL.

### <a name="azure-database-for-mysql-as-source"></a>База данных Azure для MySQL в качестве источника

Чтобы скопировать данные из базы данных Azure для MySQL, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **AzureMySqlSource**. | ДА |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |
| куерикоммандтимеаут | Время ожидания до истечения времени ожидания запроса. Значение по умолчанию — 120 минут (02:00:00) | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>База данных Azure для MySQL в качестве приемника

Чтобы скопировать данные в базу данных Azure для MySQL, в разделе **приемника** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type приемника действия копирования должно иметь значение **азуремисклсинк** . | ДА |
| preCopyScript | Укажите SQL-запрос для действия копирования, который будет выполнен перед записью данных в базу данных Azure для MySQL при каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |
| writeBatchSize | Вставляет данные в таблицу базы данных Azure для MySQL, когда размер буфера достигает writeBatchSize.<br>Допустимое значение — целое число, представляющее количество строк. | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br>Допустимые значения: временной диапазон. Например, 00:30:00 (30 минут). | Нет (значение по умолчанию — 00:00:30) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Сопоставление типов данных для базы данных Azure для MySQL

При копировании данных из базы данных Azure для MySQL для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных MySQL. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных базы данных Azure для MySQL | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
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
| `year` |`Int32` |

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
