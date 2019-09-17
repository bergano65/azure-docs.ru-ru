---
title: Копирование данных в базу данных Azure для PostgreSQL и из нее с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные в базу данных Azure для PostgreSQL и обратно с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: d6b860f43abae2283b4889bff0913bac25c821f5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71017781"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-using-azure-data-factory"></a>Копирование данных в базу данных Azure для PostgreSQL и из нее с помощью фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Azure для PostgreSQL. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

Этот соединитель является специализированным для [службы "база данных Azure для PostgreSQL](../postgresql/overview.md)". Чтобы скопировать данные из общей базы данных PostgreSQL, расположенной в локальной среде или в облаке, используйте [соединитель PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель базы данных Azure для PostgreSQL поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой исходной матрицей](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из базы данных Azure для PostgreSQL можно скопировать в любое поддерживаемое хранилище данных, которое используется в качестве приемника. Вы также можете скопировать данные из любого поддерживаемого исходного хранилища данных в базу данных Azure для PostgreSQL. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю базы данных Azure для PostgreSQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных Azure для PostgreSQL поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **AzurePostgreSql**. | Да |
| connectionString | Строка подключения к базе данных Azure для PostgreSQL через интерфейс ODBC.<br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `password` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

Типичная строка подключения — `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | Описание | Параметры | Обязательное значение |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Метод, используемый драйвером для шифрования данных, отправленных между драйвером и сервером базы данных. Например,`EncryptionMethod=<0/1/6>;`| 0 (без шифрования) **(по умолчанию)** -1 (SSL) или 6 (RequestSSL) | Нет |
| ValidateServerCertificate (VSC) | Определяет, проверяет ли драйвер сертификат, отправленный сервером базы данных, когда включено шифрование SSL (метод шифрования = 1). Например,`ValidateServerCertificate=<0/1>;`| 0 (отключено) **(по умолчанию)** -1 (включено) | Нет |

**Пример.**

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

**Пример: хранение пароля в Azure Key Vault**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных базы данных Azure для PostgreSQL.

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для свойства type набора данных значение **AzurePostgreSqlTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для набора данных необходимо задать значение **AzurePostgreSqlTable**. | Да |
| tableName | Имя таблицы. | Нет (если свойство query указано в источнике действия) |

**Пример**

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником базы данных Azure для PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>База данных Azure для PostgreSQL в качестве источника

Чтобы скопировать данные из базы данных Azure для PostgreSQL, задайте для типа источника в действии копирования значение **AzurePostgreSqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type источника действия копирования необходимо задать значение **AzurePostgreSqlSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

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
| type | Свойство type приемника действия копирования должно иметь следующее значение: **азурепостгресклсинк** | Да |
| preCopyScript | Укажите SQL-запрос для действия копирования, который будет выполнен перед записью данных в базу данных Azure для PostgreSQL в каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |
| writeBatchSize | Вставляет данные в таблицу базы данных Azure для PostgreSQL, когда размер буфера достигает writeBatchSize.<br>Допустимое значение — целое число, представляющее количество строк. | Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br>Допустимые значения: временной диапазон. Например, 00:30:00 (30 минут). | Нет (значение по умолчанию — 00:00:30) |

**Пример.**

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

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
