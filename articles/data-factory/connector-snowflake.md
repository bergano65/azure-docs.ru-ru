---
title: Копирование данных из и в снежинку
description: Узнайте, как копировать данные из и в снежинки с помощью фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 347f37fb999656a1c4951f01a75a392887b5b882
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045677"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Копирование данных из «снежинка» и обратно с помощью фабрики данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из «снежинка» и обратно. Дополнительные сведения о фабрике данных см. в [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Соединитель «снежинка» поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой таблицей источника или приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Для действия копирования этот соединитель «снежинка» поддерживает следующие функции:

- Копирование данных из снежинки, которое использует команду [Копировать в [Расположение]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) по схеме «снежинка» для достижения наилучшей производительности.
- Скопируйте данные в «снежинка», используя преимущества команды « [Копировать в [Таблица]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) » «снежинка» для достижения наилучшей производительности. Он поддерживает снежинки в Azure.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, определяющих сущности фабрики данных, относящиеся к соединителю «снежинка».

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы «снежинка» поддерживаются следующие свойства.

| Свойство.         | Описание                                                  | Обязательно |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | Для свойства Type необходимо задать значение « **снежинка**».              | Да      |
| connectionString | Настройте [полное имя учетной записи](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (включая дополнительные сегменты, которые указывают регион и облачную платформу), имя пользователя, пароль, базу данных и хранилище. Укажите строку подключения JDBC для подключения к экземпляру снежинки. Также можно добавить пароль в Azure Key Vault. Дополнительные сведения см. в примерах, приведенных ниже в таблице, а также в статье [учетные данные хранения Azure Key Vault](store-credentials-in-key-vault.md) .| Да      |
| connectVia       | [Среда выполнения интеграции](concepts-integration-runtime.md) , используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции (если хранилище данных находится в частной сети). Если не указано, используется среда выполнения интеграции Azure по умолчанию. | Нет       |

**Пример**.

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>(optional)"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пароль в Azure Key Vault**.

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>(optional)",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

Для набора данных «снежинка» поддерживаются следующие свойства.

| Свойство.  | Описание                                                  | Обязательно                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Свойство Type набора данных должно иметь значение **сновфлакетабле**. | Да                         |
| схема | Имя схемы. |Нет для источника, да для приемника  |
| table | Имя таблицы или представления. |Нет для источника, да для приемника  |

**Пример**.

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником «снежинка».

### <a name="snowflake-as-the-source"></a>Снежинки в качестве источника

Соединитель «снежинка» использует команду [Copy в [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) «снежинка» для достижения наилучшей производительности.

Если хранилище данных приемника и формат изначально поддерживаются командой «Копировать», можно использовать действие копирования для непосредственного копирования из снежинки в приемник. Дополнительные сведения см. в разделе [непосредственная копия из снежинки](#direct-copy-from-snowflake). В противном случае используйте встроенную [промежуточную копию из снежинки](#staged-copy-from-snowflake).

Чтобы скопировать данные из снежинки, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство.                     | Описание                                                  | Обязательно |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Свойство Type источника действия копирования должно иметь значение **сновфлакесаурце**. | Да      |
| query          | Указывает SQL запрос для чтения данных из снежинки.<br>Исполнение хранимой процедуры не поддерживается. | Нет       |
| exportSettings | Дополнительные параметры, используемые для получения данных из снежинки. Можно настроить те, которые поддерживаются командой Копировать в, которую будет передавать фабрика данных при вызове инструкции. | Нет       |
| ***В разделе `exportSettings` :*** |  |  |
| тип | Тип команды экспорта, установленный в **сновфлакикспорткопикомманд**. | Да |
| аддитионалкопйоптионс | Дополнительные параметры копирования, предоставляемые в виде словаря пар "ключ-значение". Примеры: MAX_FILE_SIZE, OVERWRITE. Дополнительные сведения см. в разделе [Параметры копирования «снежинка](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)». | Нет |
| аддитионалформатоптионс | Дополнительные параметры формата файла, предоставляемые для копирования команды в виде словаря пар "ключ-значение". Примеры: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Дополнительные сведения см. в разделе [Параметры формата типа "снежинка](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)". | Нет |

#### <a name="direct-copy-from-snowflake"></a>Прямая копия из снежинки

Если хранилище данных приемника и формат соответствуют критериям, описанным в этом разделе, можно использовать действие копирования для непосредственного копирования из снежинки в приемник. Фабрика данных проверяет параметры и не выполняет действие копирования, если не выполняются следующие условия.

- **Связанная служба приемника** — это [**хранилище BLOB-объектов Azure**](connector-azure-blob-storage.md) с проверкой подлинности **подписанного общего доступа** .

- **Формат данных приемника** — **Parquet** или **текст с разделителями**, со следующими конфигурациями:

   - Для формата **Parquet** кодек сжатия — **None**, **привязки**или **LZO**.
   - Для **текстового формата с разделителями** :
     - `rowDelimiter`является **\r\n**или любым отдельным символом.
     - `compression`не может иметь **Сжатие**, **gzip**, **bzip2**или **Deflate**.
     - В параметре `encodingName` оставляется значение по умолчанию или задается значение **utf-8**.
     - `quoteChar`**двойная кавычка**, **одинарная кавычка**или **пустая строка** (без кавычек).
- В источнике действия копирования `additionalColumns` не указано.
- Сопоставление столбцов не указано.

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Промежуточное копирование из снежинки

Если хранилище данных приемника или формат не совместимы с командой снежинка COPY, как упоминалось в последнем разделе, включите встроенную промежуточную копию с использованием промежуточного экземпляра хранилища больших двоичных объектов Azure. Промежуточное копирование также обеспечивает лучшую пропускную способность. Фабрика данных экспортирует данные из снежинки в промежуточное хранилище, затем копирует данные в приемник и, наконец, очищает временные данные из промежуточного хранилища. Дополнительные сведения о копировании данных с помощью промежуточного хранения см. в разделе [поэтапное копирование](copy-activity-performance-features.md#staged-copy) .

Чтобы использовать эту функцию, создайте [связанную службу хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties) , которая ссылается на учетную запись хранения Azure в качестве промежуточного промежуточного хранения. Затем укажите `enableStaging` Свойства и `stagingSettings` в действии копирования.

> [!NOTE]
> Связанная служба хранилища BLOB-объектов Azure должна использовать проверку подлинности подписанного URL-доступа, как это требуется для команды копирования по схеме "Снежинка". 

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Снежинки в качестве приемника

Соединитель «снежинка» использует команду [Copy в [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) «снежинка» для достижения наилучшей производительности. Он поддерживает запись данных в снежинки в Azure.

Если исходное хранилище данных и формат поддерживаются командой снежинка COPY, можно использовать действие копирования для непосредственного копирования из источника в снежинки. Дополнительные сведения см. [в разделе непосредственная копия в снежинки](#direct-copy-to-snowflake). В противном случае используйте встроенное [промежуточное копирование в снежинки](#staged-copy-to-snowflake).

Чтобы скопировать данные в снежинку, в разделе **приемник** действия копирования поддерживаются следующие свойства.

| Свойство.          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Свойство Type приемника действия копирования, для которого задано значение **сновфлакесинк**. | Да                                           |
| preCopyScript     | Укажите SQL-запрос для выполнения действия копирования перед записью данных в «снежинка» в каждом запуске. Это свойство используется для очистки предварительно загруженных данных. | нет                                            |
| importSettings | Дополнительные параметры, используемые для записи данных в снежинки. Можно настроить те, которые поддерживаются командой Копировать в, которую будет передавать фабрика данных при вызове инструкции. | Нет |
| ***В разделе `importSettings` :*** |                                                              |  |
| тип | Тип команды импорта, установленный в **сновфлакеимпорткопикомманд**. | Да |
| аддитионалкопйоптионс | Дополнительные параметры копирования, предоставляемые в виде словаря пар "ключ-значение". Примеры: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Дополнительные сведения см. в разделе [Параметры копирования «снежинка](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)». | Нет |
| аддитионалформатоптионс | Дополнительные параметры формата файла, предоставляемые команде COPY, предоставленные в виде словаря пар "ключ-значение". Примеры: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Дополнительные сведения см. в разделе [Параметры формата типа "снежинка](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)". | Нет |

#### <a name="direct-copy-to-snowflake"></a>Прямая копия в снежинку

Если исходное хранилище данных и формат соответствуют критериям, описанным в этом разделе, можно использовать действие копирования для непосредственного копирования из источника в снежинки. Фабрика данных Azure проверяет параметры и завершает выполнение действия копирования, если не выполняются следующие условия.

- **Исходная связанная служба** — это [**хранилище BLOB-объектов Azure**](connector-azure-blob-storage.md) с проверкой подлинности **подписанного общего доступа** .

- **Исходный формат данных** — **Parquet** или **текст с разделителями**, со следующими конфигурациями:

   - Для формата **Parquet** кодек сжатия — **None** или **привязки**.

   - Для **текстового формата с разделителями** :
     - `rowDelimiter`является **\r\n**или любым отдельным символом. Если разделитель строк не равен «\r\n», то `firstRowAsHeader` он должен иметь **значение false**и `skipLineCount` не указан.
     - `compression`не может иметь **Сжатие**, **gzip**, **bzip2**или **Deflate**.
     - `encodingName`принимает значение по умолчанию или имеет значение "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "Windows-1251", "Windows-1252", "Windows-1253", "WINDOWS-1254", "Windows-1255".
     - `quoteChar`**двойная кавычка**, **одинарная кавычка**или **пустая строка** (без кавычек).

- В источнике действия копирования: 

   -  `additionalColumns` не задан.
   - Если источником является папка, то параметру присваивается `recursive` значение true.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` не указаны.

**Пример.**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Промежуточное копирование в снежинку

Если хранилище данных приемника или формат не совместимы с командой снежинка COPY, как упоминалось в последнем разделе, включите встроенную промежуточную копию с использованием промежуточного экземпляра хранилища больших двоичных объектов Azure. Промежуточное копирование также обеспечивает лучшую пропускную способность. Фабрика данных автоматически преобразует данные в соответствии с требованиями «снежинка» формата данных. Затем он вызывает команду COPY для загрузки данных в снежинку. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Дополнительные сведения о копировании данных с помощью промежуточного хранения см. в разделе [поэтапное копирование](copy-activity-performance-features.md#staged-copy) .

Чтобы использовать эту функцию, создайте [связанную службу хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties) , которая ссылается на учетную запись хранения Azure в качестве промежуточного промежуточного хранения. Затем укажите `enableStaging` Свойства и `stagingSettings` в действии копирования.

> [!NOTE]
> Для связанной службы хранилища BLOB-объектов Azure необходимо использовать проверку подлинности подписанного URL-доступа, как это требуется для команды копирования по схеме "Снежинка".

**Пример**.

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Дополнительные сведения о свойствах см. в разделе [действие поиска](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Дальнейшие шаги

Список хранилищ данных, поддерживаемых в качестве источников и приемников в рамках действия копирования в фабрике данных, см. в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).
