---
title: Копирование данных из Google Cloud Storage с помощью Фабрики данных Azure
description: Узнайте, как копировать данные из Google Cloud Storage в поддерживаемые хранилища данных, выступающие в роли приемников, с помощью Фабрики данных Azure.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2ee0fc7f7b7e3ef465a43eed2bd47f33e87162b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417305"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Копирование данных из Google Cloud Storage с помощью Фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается, как копировать данные из облачного хранилища Google. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем облачного хранилища Google поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Удаление действия](delete-activity.md)

В частности, этот соединитель Google Cloud Storage поддерживает копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>При копировании данных из Google Cloud Storage используется [соединитель Amazon S3](connector-amazon-simple-storage-service.md) с соответствующей настраиваемой конечной точкой S3, так как Google Cloud Storage обеспечивает совместимость с S3.

## <a name="prerequisites"></a>Предварительные требования

В учетной записи облачного хранения Google требуется следующая настройка:

1. Обеспечить совместимость учетной записи облачного хранения Google
2. Установите проект по умолчанию, содержащий данные, которые необходимо скопировать
3. Создайте ключ доступа.

![Извина ключ доступа для облачного хранения Google](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скопировать данные из Google Cloud Storage, убедитесь, что вы предоставили следующие разрешения:

- **Для выполнения копирования:**: `s3:GetObject` и `s3:GetObjectVersion` для операций с объектом.
- **Для авторизации графического интерфейса Data Factory:** `s3:ListAllMyBuckets` а `s3:ListBucket` / `s3:GetBucketLocation` для разрешений на операции вковечки дополнительно требуются для таких операций, как подключение к тесту и просмотр/навигация по пути файла. Если вы не хотите предоставлять эти разрешения, пропустите проверку соединения на странице создания связанной службы и укажите путь непосредственно в параметрах набора данных.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к Google Cloud Storage.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google Cloud Storage поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено в **GoogleCloudStorage.** | Да |
| accessKeyId | Идентификатор секретного ключа доступа. Чтобы найти ключ доступа и секрет, [см.](#prerequisites) |Да |
| secretAccessKey | Сам секретный ключ доступа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| serviceUrl | Укажите пользовательскую конечную **`https://storage.googleapis.com`** точку S3 как. | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

Например:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `location` облачного хранения Google в настройках в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа, под `location` аедоиевая в наборе данных, должно быть установлено на **AmazonS3Location.** | Да      |
| bucketName | Имя контейнера S3.                                          | Да      |
| folderPath | Путь к папке под данным ведром. Если требуется использовать подстановочный знак для фильтрации папки, пропустите эту настройку и укажите в настройках источника активности. | нет       |
| fileName   | Имя файла под данном упором и папкойPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите эту настройку и укажите в настройках источника активности. | нет       |

**Примере:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником данных Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `storeSettings` облачного хранения Google в настройках в формате на основе источника копий:

| Свойство                 | Описание                                                  | Обязательно                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **AmazonS3ReadSettings.** | Да                                                         |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Разрешенные значения **верны** (по умолчанию) и **ложные.** | нет                                                          |
| prefix                   | Префикс для ключа объекта S3 под данным ведром, настроенным в наборе данных для фильтрации объектов источника. Выбираются объекты, ключи которых начинаются с этого префикса. Применяется только `wildcardFolderPath` `wildcardFileName` тогда, когда и свойства не указаны. |                                                             |
| подстановочный знакFolderPath       | Путь папки с символами подстановочных знаков под данным ведром, настроенным в наборе данных для фильтрации папок источника. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                                          |
| подстановочный знакFileName         | Имя файла с символами подстановочных знаков под данным ведром и папкойPath/wildcardFolderPath для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, `fileName` если в `prefix` наборе данных и не указаны |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | нет                                                          |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                                          |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет                                                          |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| bucket | ключ | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные полужирным шрифтом)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*` | Да | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*.csv` | Да | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Свойства активности GetMetadata

Чтобы узнать подробности о свойствах, проверьте [активность GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Удаление свойств активности

Чтобы узнать подробности о свойствах, проверьте [действия Delete](delete-activity.md)

## <a name="legacy-models"></a>Устаревшие модели

>[!NOTE]
>Следующие модели по-прежнему поддерживаются как для обратной совместимости. Вы предложили использовать новую модель, упомянутую в вышеуказанных разделах в будущем, и ADF авторство UI переключился на генерацию новой модели.

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа для набора данных должно быть: **AmazonS3Object**. |Да |
| bucketName | Имя контейнера S3. Фильтр подстановочных знаков не поддерживается. |Yes для действия Copy/Lookup, No для действия GetMetadata |
| ключ | **Имя или фильтр подстановочных знаков** ключа объекта S3 в указанном контейнере. Применяется, только если свойство prefix не указано. <br/><br/>Фильтр с подстановочными знаками поддерживается для пути к папке и имени файла. Допустимые знаки подстановки: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"key": "rootfolder/subfolder/*.csv"`<br/>Пример 2. `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). Используйте `^` для экранирования знаков, если фактическое имя файла или папки содержит подстановочный знак или этот escape-символ. |нет |
| prefix | Префикс для ключа объекта S3. Выбираются объекты, ключи которых начинаются с этого префикса. Применяется, только если свойство key не указано. |нет |
| version | Версия объекта S3, если включено управление версиями S3. |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если вы хотите **копировать файлы как между** файлохранилищами (двоичная копия), пропустите раздел формата как в водах, так и в определениях набора выходных данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs-legacy.md#text-format), [формате Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **ГЗип,** **Дефлировать**, **B'ip2**, и **цифферлат**.<br/>Поддерживаемые уровни: **Оптимальный** и **Быстрый**. |нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите **bucketName** для контейнеров и **prefix** для части папки.<br>Чтобы скопировать один файл с заданным именем, укажите **bucketName** для контейнеров и **key** для части папки вместе с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **bucketName** для контейнеров и **key** для части папки и фильтра подстановочных знаков.

**Пример. Использование префикса**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Модель источника исходной деятельности устаревшей копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **FileSystemSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Разрешенные значения: **правда** (по умолчанию), **ложные** | нет |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
В статье [Действие копирования в Фабрике данных Azure](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
