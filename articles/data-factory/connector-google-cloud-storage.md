---
title: Копирование данных из Google Cloud Storage с помощью Фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Google Cloud Storage в поддерживаемые хранилища данных, выступающие в роли приемников, с помощью Фабрики данных Azure.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jingwang
ms.openlocfilehash: 815ee569f0919f32b38b7b7cdf848be184b7aea8
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661859"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Копирование данных из Google Cloud Storage с помощью Фабрики данных Azure

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные из Google Cloud Storage. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете копировать данные Google Cloud Storage в любое хранилище данных, выступающее в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Google Cloud Storage поддерживает копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>При копировании данных из Google Cloud Storage используется [соединитель Amazon S3](connector-amazon-simple-storage-service.md) с соответствующей настраиваемой конечной точкой S3, так как Google Cloud Storage обеспечивает совместимость с S3.

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скопировать данные из Google Cloud Storage, убедитесь, что вы предоставили следующие разрешения:

- **Для выполнения операции копирования** — `s3:GetObject` и `s3:GetObjectVersion` для операций с объектами.
- **Для создания графического пользовательского интерфейса Фабрики данных** — разрешения `s3:ListAllMyBuckets` и `s3:ListBucket`/`s3:GetBucketLocation`, также требуются для выполнения в контейнере операций, таких как проверка соединения, просмотр путей к файлам и переход по ним. Если вы не хотите предоставлять эти разрешения, пропустите проверку соединения на странице создания связанной службы и укажите путь непосредственно в параметрах набора данных.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к Google Cloud Storage.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Google Cloud Storage поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства типа необходимо задать значение **AmazonS3**. | Yes |
| accessKeyId | Идентификатор секретного ключа доступа. Чтобы найти ключ доступа и секрет, перейдите в раздел **Google Cloud Storage** > **Параметры** > **Взаимодействие**. |Yes |
| secretAccessKey | Сам секретный ключ доступа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| serviceUrl | Укажите настраиваемую конечную точку S3 в формате **`https://storage.googleapis.com`**. | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

Вот пример: 

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Google Cloud Storage.

Чтобы скопировать данные из Google Cloud Storage, установите свойство типа набора данных **AmazonS3Object**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type набора данных необходимо задать следующее значение: **AmazonS3Object** |Yes |
| bucketName | Имя контейнера S3. Фильтр подстановочных знаков не поддерживается. |Yes для действия Copy/Lookup, No для действия GetMetadata |
| key | **Имя или фильтр подстановочных знаков** ключа объекта S3 в указанном контейнере. Применяется, только если свойство prefix не указано. <br/><br/>Фильтр с подстановочными знаками поддерживается для пути к папке и имени файла. Допустимые знаки подстановки: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"key": "rootfolder/subfolder/*.csv"`<br/>Пример 2. `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). Используйте `^` для экранирования знаков, если фактическое имя файла или папки содержит подстановочный знак или этот escape-символ. |Нет  |
| prefix | Префикс для ключа объекта S3. Выбираются объекты, ключи которых начинаются с этого префикса. Применяется, только если свойство key не указано. |Нет  |
| версия | Версия объекта S3, если включено управление версиями S3. |Нет  |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет  |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет  |
| свойства | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать или создать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Оптимальный** и **Самый быстрый**. |Нет  |

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

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником данных Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage в качестве источника

Чтобы копировать данные из Google Cloud Storage, установите тип источника **FileSystemSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь следующее значение: **FileSystemSource**. |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет  |

**Пример.**

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

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| bucket | key | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные полужирным шрифтом)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*` | Да | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| bucket | `Folder*/*.csv` | Да | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="next-steps"></a>Дополнительная информация
В статье [Действие копирования в Фабрике данных Azure](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
