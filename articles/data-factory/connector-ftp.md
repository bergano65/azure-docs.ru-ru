---
title: Копирование данных с FTP-сервера с использованием фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные с FTP-сервера на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 0e1127d90aeb4c59687ac4df7fb7ebae1901cee8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228428"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Копирование данных с FTP-сервера с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которую вы используете:"]
>
> * [Версия 1](v1/data-factory-ftp-connector.md)
> * [Текущая версия](connector-ftp.md)

В этой статье описывается, как копировать данные с FTP-сервера. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель FTP поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживается матрицы источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

В частности, этот соединитель FTP поддерживает:

- Копирование файлов с использованием **базовой** или **анонимной** проверки подлинности.
- Копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к FTP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы FTP поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать значение **FtpServer**. | Да |
| host | Укажите имя или IP-адрес FTP-сервера. | Да |
| port | Укажите порт, прослушиваемый FTP-сервером.<br/>Допустимые значения: целые числа; значение по умолчанию — **21**. | Нет |
| enableSsl | Укажите, какой канал следует использовать (FTP через SSL или TLS).<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |
| enableServerCertificateValidation | Укажите, следует ли включать проверку SSL-сертификата на сервере при использовании канала FTP через SSL или TLS.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |
| authenticationType | Укажите тип проверки подлинности.<br/>Допустимые значения: **Basic** (обычная) и **Anonymous** (анонимная). | Да |
| userName | Укажите пользователя, имеющего доступ к FTP-серверу. | Нет |
| password | Укажите пароль для пользователя (userName). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

>[!NOTE]
>Соединитель FTP поддерживает доступ к FTP-серверу без шифрования или с явным шифрованием SSL/TLS. Он не поддерживает неявное шифрование SSL/TLS.

**Пример 1. Использование анонимной проверки подлинности**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Использование базовой проверки подлинности**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

- Для **Parquet и текстовый формат с разделителями**, см. [Parquet и текстовый файл с разделителями, формат набора данных](#parquet-and-delimited-text-format-dataset) раздел.
- Для других форматов, например **формата ORC и Avro/JSON/двоичных файлов**, см. [другой набор данных в формате](#other-format-dataset) раздел.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet и набор данных формат с разделителями

Чтобы скопировать данные из FTP в **Parquet или текстовый формат с разделителями**, см. [формат Parquet](format-parquet.md) и [текстовый формат с разделителями](format-delimited-text.md) статья на основе формат набора данных и поддерживаемых параметров . Поддерживаются следующие свойства, для FTP-сервера в разделе `location` параметры в наборе данных на основе формата:

| Свойство   | ОПИСАНИЕ                                                  | Обязательно для заполнения |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа в списке `location` в наборе данных должно быть присвоено **FtpServerLocation**. | Да      |
| folderPath | Путь к папке. Если вы хотите использовать подстановочный знак в папку фильтра, пропустить этот параметр и укажите в параметрах исходного действия. | Нет       |
| fileName   | Имя файла, в разделе данной folderPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите этот параметр и укажите в параметрах исходного действия. | Нет       |

> [!NOTE]
> **Общая папка** типа набора данных в формате Parquet или текст, описанные в следующем разделе по-прежнему поддерживается в качестве-предназначен для действий копирования и поиска/GetMetadata для обеспечения обратной совместимости. Рекомендуется использовать эту новую модель, в дальнейшем и разработки пользовательского интерфейса ADF изменился на создание этих новых типов.

**Пример.**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Другой набор данных в формате

Чтобы скопировать данные из FTP в **формата ORC и Avro/JSON/двоичных файлов**, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **FileShare**. |Yes |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактической папки содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Yes |
| fileName | **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ. |Нет |
| format | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Оптимальный** и **Самый быстрый**. |Нет |
| useBinaryTransfer | Укажите, следует ли использовать режим передачи в двоичном формате. Задается значение true, если следует использовать двоичный формат (по умолчанию), и false, если следует использовать ASCII. |Нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

>[!NOTE]
>Если вы использовали свойство fileFilter для фильтрации файлов, оно по-прежнему поддерживается без изменений, а вам предлагается далее использовать новую возможность фильтрации, добавленную к fileName.

**Пример.**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником FTP.

### <a name="ftp-as-source"></a>FTP в качестве источника

- Для копирования из **Parquet и текстовый формат с разделителями**, см. [Parquet и исходный формат с разделителями](#parquet-and-delimited-text-format-source) раздел.
- Для копирования из других форматов, например **формата ORC и Avro/JSON/двоичных файлов**, см. [другого формата источника](#other-format-source) раздел.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet и исходный формат с разделителями

Чтобы скопировать данные из FTP в **Parquet или текстовый формат с разделителями**, см. [формат Parquet](format-parquet.md) и [текстовый формат с разделителями](format-delimited-text.md) статьи на источника действия копирования на основе формата и Поддерживаемые параметры. Поддерживаются следующие свойства, для FTP-сервера в разделе `storeSettings` параметры источника копирования на основе формата:

| Свойство                 | ОПИСАНИЕ                                                  | Обязательно для заполнения                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство типа в списке `storeSettings` должно быть присвоено **FtpReadSetting**. | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет                                            |
| wildcardFolderPath       | Путь к папке с использованием подстановочных знаков для фильтрации исходных папках. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Нет                                            |
| wildcardFileName         | Имя файла с использованием подстановочных знаков в заданной folderPath/wildcardFolderPath к исходным файлам фильтра. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, в том случае, если `fileName` не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | Нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | Нет                                            |
| useBinaryTransfer        | Укажите, следует ли использовать режим передачи в двоичном формате для хранилищ FTP. Задается значение true, если следует использовать двоичный формат (по умолчанию), и false, если следует использовать ASCII. | Нет                                            |
| maxConcurrentConnections | Число подключений для подключения к хранилищу хранилища, одновременно. Укажите только в том случае, если вы хотите ограничить максимальное количество одновременных подключений к хранилищу данных. | Нет                                            |

> [!NOTE]
> Для Parquet/текстового формата с разделителями **FileSystemSource** источника действия копирования типа, описанные в следующем разделе по-прежнему поддерживается в качестве-— для обеспечения обратной совместимости. Рекомендуется использовать эту новую модель, в дальнейшем и разработки пользовательского интерфейса ADF изменился на создание этих новых типов.

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromFTP",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FtpReadSetting",
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

#### <a name="other-format-source"></a>Другой источник формат

Чтобы скопировать данные из FTP в **формата ORC и Avro/JSON/двоичных файлов**, в действии копирования поддерживаются следующие свойства **источника** раздел:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь следующее значение: **FileSystemSource**. |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |
| maxConcurrentConnections | Число подключений для подключения к хранилищу хранилища, одновременно. Укажите только в том случае, если вы хотите ограничить максимальное количество одновременных подключений к хранилищу данных. | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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

| folderPath | fileName | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные **полужирным** шрифтом)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (пусто, используйте по умолчанию) | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | (пусто, используйте по умолчанию) | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
