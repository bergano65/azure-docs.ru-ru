---
title: Копирование данных в базу данных Azure для PostgreSQL и из нее
description: Узнайте, как копировать данные в базу данных Azure для PostgreSQL и обратно с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.openlocfilehash: 3729d43716a0f4e133fb175da2546aac560b0525
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931613"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure для PostgreSQL и обратно с помощью фабрики данных Azure

В этой статье описывается, как с помощью функции копирования в фабрике данных Azure копировать данные из базы данных Azure для PostgreSQL. Эта статья посвящена [действию копирования в фабрике данных Azure](copy-activity-overview.md) , в котором представлен общий обзор действий копирования.

Этот соединитель является специализированным для [службы "база данных Azure для PostgreSQL](../postgresql/overview.md)". Чтобы скопировать данные из универсальной базы данных PostgreSQL, расположенной в локальной среде или в облаке, используйте [соединитель PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель базы данных Azure для PostgreSQL поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из базы данных Azure для PostgreSQL можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Вы также можете скопировать данные из любого поддерживаемого исходного хранилища данных в базу данных Azure для PostgreSQL. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, см. в таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) .

В Фабрике данных Azure предоставляется встроенный драйвер, который обеспечивает подключение. Поэтому вам не нужно вручную устанавливать драйверы для использования этого соединителя.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю базы данных Azure для PostgreSQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных Azure для PostgreSQL поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства Type необходимо задать значение **азурепостгрескл**. | ДА |
| connectionString | Строка подключения к базе данных Azure для PostgreSQL через интерфейс ODBC.<br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Можно также добавить пароль в Azure Key Vault и извлечь `password`ную конфигурацию из строки подключения. Для получения дополнительных сведений см. следующие примеры и [Сохраните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md) . | ДА |
| connectVia | Это свойство представляет [среду выполнения интеграции](concepts-integration-runtime.md) , используемую для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

Типичная строка подключения — `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Ниже приведены дополнительные свойства, которые можно задать в любом случае.

| Свойство | Описание | Параметры | Обязательно для заполнения |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Метод, используемый драйвером для шифрования данных, отправленных между драйвером и сервером базы данных. Например, `EncryptionMethod=<0/1/6>;`| 0 (без шифрования) **(по умолчанию)** -1 (SSL) или 6 (RequestSSL) | Нет |
| ValidateServerCertificate (VSC) | Определяет, проверяет ли драйвер сертификат, отправленный сервером базы данных при включенном шифровании SSL (метод шифрования = 1). Например, `ValidateServerCertificate=<0/1>;`| 0 (отключено) **(по умолчанию)** -1 (включено) | Нет |

**Пример**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Пример**:

***Хранение пароля в Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
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

Полный список разделов и свойств, доступных для определения наборов данных, см. [в статье DataSets in Azure Data фабрика](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, которые служба "база данных Azure для PostgreSQL" поддерживает в DataSets.

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для свойства type набора данных значение **AzurePostgreSqlTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type набора данных должно иметь значение **азурепостгресклтабле** | ДА |
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

Полный список разделов и свойств, доступных для определения действий, см. [в статье конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником базы данных Azure для PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>База данных Azure для PostgreSQL в качестве источника

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для типа источника в действии копирования значение **AzurePostgreSqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type источника действия копирования должно иметь значение **AzurePostgreSqlSource** . | ДА |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например: `"SELECT * FROM MyTable"` | Нет (если указано свойство tableName в наборе данных) |

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

### <a name="azure-database-for-postgresql-as-sink"></a>База данных Azure для PostgreSQL в качестве приемника

Чтобы скопировать данные в базу данных Azure для PostgreSQL, в разделе **приемника** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type приемника действия копирования должно иметь значение **азурепостгресклсинк**. | ДА |
| preCopyScript | Укажите SQL-запрос для действия копирования, который необходимо выполнить, прежде чем записывать данные в базу данных Azure для PostgreSQL в каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |
| writeBatchSize | Вставляет данные в таблицу базы данных Azure для PostgreSQL, когда размер буфера достигает writeBatchSize.<br>Допустимое значение — это целое число, представляющее количество строк. | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br>Допустимые значения — строки TimeSpan. Например, 00:30:00 (30 минут). | Нет (значение по умолчанию — 00:00:30) |

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

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Дополнительные сведения о свойствах см. [в разделе действие поиска в фабрике данных Azure](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure см. в [этой таблице](copy-activity-overview.md#supported-data-stores-and-formats).
