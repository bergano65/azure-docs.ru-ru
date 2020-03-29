---
title: Копирование данных в и из базы данных Azure для PostgreS'L
description: Узнайте, как скопировать данные в базу данных Azure и из базы данных PostgreS'L с помощью копирования в конвейере Фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444377"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure и из базы данных PostgreS'L с помощью Azure Data Factory

В этой статье описывается, как использовать функцию активности копирования на фабрике данных Azure для копирования данных из базы данных Azure для PostgreS'L. Он основывается на активности Copy в статье [Azure Data Factory,](copy-activity-overview.md) в которой представлен общий обзор активности копирования.

Этот разъем специализируется на [базе данных Azure для службы PostgreS'L.](../postgresql/overview.md) Для копирования данных из общей базы данных PostgreS'L, расположенной в помещении или в облаке, используйте [разъем PostgreS'L.](connector-postgresql.md)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Эта база данных Azure для разъема PostgreS'L поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из базы данных Azure для PostgreSQL можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Кроме того, можно скопировать данные из любого поддерживаемого хранилища исходных данных в базу данных Azure database для PostgreS'L. Для списка хранилиров данных, которые активность копирования [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) поддерживает в качестве источников и раковин, см.

В Фабрике данных Azure предоставляется встроенный драйвер, который обеспечивает подключение. Таким образом, вам не нужно вручную установить любой драйвер, чтобы использовать этот разъем.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приведены сведения о свойствах, используемых для определения сущностей фабрики данных, специфичных для базы данных Azure для разъема PostgreS-L.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы, связанной с Azure Database для службы PostgreS'L:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено: **AzurePostgreSql**. | Да |
| connectionString | Строка подключения к базе данных Azure для PostgreSQL через интерфейс ODBC.<br/>Вы также можете поместить пароль в Хранилище `password` ключей Azure и вытащить конфигурацию из строки соединения. Для получения более подробной информации в Azure Key Vault можно ознакомиться со следующими образцами и [учетными данными магазина в Azure Key Vault.](store-credentials-in-key-vault.md) | Да |
| connectVia | Это свойство представляет [время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

Типичная строка подключения — `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Вот еще свойства, которые вы можете установить в вашем случае:

| Свойство | Описание | Параметры | Обязательно |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Метод, используемый драйвером для шифрования данных, отправленных между драйвером и сервером базы данных. Например,  `EncryptionMethod=<0/1/6>;`| 0 (без шифрования) **(по умолчанию)** -1 (SSL) или 6 (RequestSSL) | нет |
| ValidateServerCertificate (VSC) | Определяет, проверяет ли драйвер сертификат, отправленный сервером базы данных при включении sSL-шифрования (метод шифрования No1). Например,  `ValidateServerCertificate=<0/1>;`| 0 (отключено) **(по умолчанию)** -1 (включено) | нет |

**Пример**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Пример**:

***Храните пароль в Хранилище ключей Azure***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, [см.](concepts-datasets-linked-services.md) В этом разделе приводится список свойств, которые База данных Azure для PostgreS'L поддерживает в наборах данных.

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для свойства type набора данных значение **AzurePostgreSqlTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на **AzurePostgreSqlTable** | Да |
| tableName | Имя таблицы | Нет (если свойство query указано в источнике действия) |

**Пример**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, [Pipelines and activities in Azure Data Factory](concepts-pipelines-activities.md)доступных для определения деятельности, см. В этом разделе приводится список свойств, поддерживаемых базой данных Azure для источника PostgreS'L.

### <a name="azure-database-for-postgresql-as-source"></a>База данных Azure для PostgreSQL в качестве источника

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для типа источника в действии копирования значение **AzurePostgreSqlSource**. Следующие свойства поддерживаются в разделе **источника активности** копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на **AzurePostgreSqlSource** | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например: `"SELECT * FROM MyTable"` | Нет (если указанное свойство таблицыВ наборе данных) |

**Пример**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>База данных Azure для PostgreS'L как раковина

Для копирования данных в базу данных Azure для PostgreS'L в разделе **«Поглотитель** активности копирования» поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **AzurePostgreSSLSink.** | Да |
| preCopyScript | Укажите запрос на выполнение действия копии перед записью данных в базу данных Azure для PostgreS'L в каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | нет |
| writeBatchSize | Вставляет данные в базу данных Azure для таблицы PostgreS'L, когда размер буфера достигает writeBatchSize.<br>Разрешенное значение представляет собой целый ряд, представляющий количество строк. | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br>Разрешенные значения являются строками Timespan. Например, 00:30:00 (30 минут). | Нет (по умолчанию 00:00:30) |

**Пример**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Для получения дополнительной информации [Lookup activity in Azure Data Factory](control-flow-lookup-activity.md)о свойствах см.

## <a name="next-steps"></a>Дальнейшие действия
Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
