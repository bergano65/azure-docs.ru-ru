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
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758611"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Копирование данных из Google Cloud Storage с помощью Фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описано копирование данных из облачного хранилища Google (GCS). Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Соединитель облачного хранилища Google поддерживается для выполнения следующих действий.

- [Действие копирования](copy-activity-overview.md) с использованием матрицы [поддерживаемых источников и приемников](copy-activity-overview.md).
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Действие удаления](delete-activity.md)

В частности, этот соединитель Google Cloud Storage поддерживает копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md). Он использует возможность взаимодействия, обеспечиваемую совместимостью GCS с S3.

## <a name="prerequisites"></a>Предварительные требования

Необходимо настроить свою учетную запись облачного хранилища Google следующим образом.

1. Включите возможность взаимодействия для своей учетной записи облачного хранилища Google.
2. Создайте проект по умолчанию, содержащий данные, которые требуется копировать из целевого контейнера GCS.
3. Создайте учетную запись службы и определите соответствующие уровни разрешений, используя облачную систему IAM в GCP. 
4. Сгенерируйте ключи доступа для этой учетной записи службы.

![Извлеките ключ доступа для облачного хранилища Google.](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скопировать данные из облачного хранилища Google, необходимо предоставить соответствующие разрешения. Разрешения, определенные в учетной записи службы, могут содержать `storage.buckets.get`, `storage.buckets.list`, `storage.objects.get` для операций с объектами.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к Google Cloud Storage.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google Cloud Storage поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **GoogleCloudStorage**. | Да |
| accessKeyId | Идентификатор секретного ключа доступа. Сведения о том, как найти ключ доступа и секрет, приводятся в разделе [Предварительные требования](#prerequisites). |Да |
| secretAccessKey | Сам секретный ключ доступа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| serviceUrl | Укажите пользовательскую конечную точку GCS в качестве **`https://storage.googleapis.com`** . | Да |
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

Ниже перечислены свойства, которые поддерживаются для облачного хранилища Google в параметрах `location` в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойству type в разделе `location` набора данных необходимо присвоить значение **GoogleCloudStorageLocation**. | Да      |
| bucketName | Имя контейнера GCS.                                          | Да      |
| folderPath | Путь к папке в заданном контейнере. Если вы хотите использовать подстановочный знак для фильтрации папки, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |
| fileName   | Имя файла в заданном контейнере и путь к папке (folderPath). Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |

**Пример**.

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
                "type": "GoogleCloudStorageLocation",
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

Ниже перечислены свойства, которые поддерживаются для облачного хранилища Google в параметрах `storeSettings` в источнике копирования основе формата:

| Свойство                 | Описание                                                  | Обязательно                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Для свойства type в разделе `storeSettings` необходимо задать значение **GoogleCloudStorageReadSettings**. | Да                                                         |
| ***Поиск файлов для копирования*** |  |  |
| Вариант 1. Статический путь<br> | Копирование из заданного контейнера или папки/пути к файлу, которые указаны в наборе данных. Если вы хотите скопировать все файлы из контейнера или папки, дополнительно укажите `wildcardFileName` как `*`. |  |
| Вариант 2. Префикс GCS<br>- prefix | Префикс для имени ключа GCS в заданном контейнере, настроенном в наборе данных для фильтрации исходных файлов GCS. Выбираются ключи GCS, имя которых начинается с `bucket_in_dataset/this_prefix`. Он использует фильтр на стороне службы GCS, который более эффективен, чем фильтр с подстановочными знаками. | нет |
| Вариант 3. Подстановочный знак<br>- wildcardFolderPath | Путь к папке с подстановочными знаками в заданном контейнере, настроенном в наборе данных для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| Вариант 3. Подстановочный знак<br>- wildcardFileName | Имя файла с подстановочными знаками в заданном контейнере и folderPath/wildcardFolderPath для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да |
| Вариант 3. Список файлов<br>- fileListPath | Указывает, что нужно скопировать заданный набор файлов. Укажите на текстовый файл, содержащий список файлов, которые необходимо скопировать, указав по одному файлу в строке (эта строка — относительный путь к пути, заданному в наборе данных).<br/>При использовании этого параметра не указывайте имя файла в наборе данных. Ознакомьтесь с дополнительными примерами в разделе [Примеры списков файлов](#file-list-examples). |нет |
| ***Дополнительные параметры:*** |  | |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. <br>Допустимые значения: **true** (по умолчанию) и **false**.<br>Это свойство не применяется при настройке `fileListPath`. |нет |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: последнее изменение. <br>Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.<br/>Это свойство не применяется при настройке `fileListPath`. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                                          |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет                                                          |

**Пример**.

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
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="file-list-examples"></a>Примеры списков файлов

В этом разделе описывается результирующее поведение при использовании пути к списку файлов в источнике действия копирования.

Предположим, что у вас есть следующая исходная структура папок и вы хотите скопировать файлы, выделенные полужирным шрифтом:

| Пример исходной структуры                                      | Содержимое файла FileListToCopy.txt                             | Конфигурация ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Метаданные<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Файл1.csv<br>Вложенная_папка1/Файл3.csv<br>Вложенная_папка1/Файл5.csv | **В наборе данных:**<br>– Контейнер: `bucket`<br>– Путь к папке: `FolderA`<br><br>**В источнике действия копирования:**<br>– Путь к списку файлов: `bucket/Metadata/FileListToCopy.txt` <br><br>Путь к списку файлов указывает на текстовый файл в том же хранилище данных, содержащий список файлов, которые необходимо скопировать, указав по одному файлу в строке с относительным путем к пути, заданному в наборе данных. |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Подробные сведения об этих свойствах см. в статье [Действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия GetMetadata

Подробные сведения об этих свойствах см. в разделе [Действие GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Свойства действия удаления

Подробные сведения об этих свойствах см. в статье [Действие удаления](delete-activity.md)

## <a name="legacy-models"></a>Устаревшие модели

Если для копирования данных из облачного хранилища Google вы используете соединитель Amazon S3, он все еще поддерживается "как есть" для обеспечения обратной совместимости. Новую модель, упомянутую в разделах выше, рекомендуется использовать в дальнейшем. Пользовательский интерфейс создания ADF был изменен для создания новой модели.

## <a name="next-steps"></a>Дальнейшие действия
В статье [Действие копирования в Фабрике данных Azure](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
