---
title: Копирование данных из HDFS с помощью фабрики данных Azure
description: Узнайте, как копировать данные из облака или локального источника HDFS на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 49285ac4a15cb52f04f6d629d80449eb515c33fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680864"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Копирование данных из HDFS с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-hdfs-connector.md)
> * [Текущая версия](connector-hdfs.md)

В этой статье описано, как копировать данные с сервера HDFS. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель HDFS поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

В частности, этот соединитель HDFS поддерживает:

- Копирование файлов с помощью **Windows** (Kerberos) или **Anonymous** проверки подлинности.
- Копирование файлов с помощью протокола **webhdfs** или **встроенной поддержки DistCp**.
- Копирование файлов "как есть", анализ или создание файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Убедитесь, что Integration Runtime доступны **ВСЕ** адреса [сервер_узла_имен]:[порт_узла_имен] и [сервер_узла_данных]:[порт_узла_данных] кластера Hadoop. По умолчанию используются [порт_узла_имен] 50070 и [порт_узла_данных] 50075.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к HDFS.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы HDFS поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Hdfs** | Да |
| URL-адрес |URL-адрес в HDFS |Да |
| authenticationType | Допустимые значения — **Анонимная** или **Windows**. <br><br> Чтобы использовать **аутентификацию Kerberos** для соединителя HDFS, настройте локальную среду, как описано [здесь](#use-kerberos-authentication-for-hdfs-connector). |Да |
| userName |Имя пользователя для проверки подлинности Windows. Для проверки подлинности Kerberos укажите `<username>@<domain>.com`. |Да (для проверки подлинности Windows) |
| пароль |Пароль для проверки подлинности Windows. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Да (для проверки подлинности Windows) |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

**Пример: использование анонимной проверки подлинности**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: использование проверки подлинности Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для HDFS в параметрах `location` в наборе данных на основе формата:

| Свойство   | Description (Описание)                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство Type в разделе `location` в наборе данных должно иметь значение **хдфслокатион**. | Да      |
| folderPath | Путь к папке. Если вы хотите использовать подстановочный знак для фильтрации папки, пропустите этот параметр и укажите в параметрах источника действия. | Нет       |
| fileName   | Имя файла в заданной folderPath. Если вы хотите использовать подстановочные знаки для фильтрации файлов, пропустите этот параметр и укажите в параметрах источника действия. | Нет       |

**Пример**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

>[!NOTE]
>Следующая модель набора данных по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать новую модель, упомянутую выше в разделе "вперед", и пользовательский интерфейс создания ADF переключился на создание новой модели.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **FileShare**. |Да |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактического файла содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Да |
| fileName |  **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования знаков, если фактическое имя папки содержит подстановочный знак или этот escape-символ. |Нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учитывайте общую производительность перемещения данных, включив этот параметр, если требуется использовать фильтр файлов из огромных объемов файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учитывайте общую производительность перемещения данных, включив этот параметр, если требуется использовать фильтр файлов из огромных объемов файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет |
| format | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

**Пример**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником HDFS.

### <a name="hdfs-as-source"></a>HDFS в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для HDFS в параметрах `storeSettings` в источнике копирования на основе формата:

| Свойство                 | Description (Описание)                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство Type в разделе `storeSettings` должно иметь значение **хдфсреадсеттинг**. | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет                                            |
| вилдкардфолдерпас       | Путь к папке с подстановочными знаками для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Нет                                            |
| вилдкардфиленаме         | Имя файла с подстановочными знаками в заданной folderPath/Вилдкардфолдерпас для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, если `fileName` не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | Нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | Нет                                            |
| distcpSettings | Группа свойств при использовании HDFS DistCp. | Нет |
| resourceManagerEndpoint | Конечная точка диспетчер ресурсов Yarn | Да, если используется DistCp |
| tempScriptPath | Путь к папке для хранения временного командного скрипта DistCp. Файл скрипта создает фабрика данных, и после завершения задания копирования файл удаляется. | Да, если используется DistCp |
| distcpOptions | Дополнительные параметры для команды DistCp. | Нет |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу хранилища. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет                                            |

**Пример**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
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

#### <a name="legacy-source-model"></a>Устаревшая исходная модель

>[!NOTE]
>Следующая исходная модель копирования по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать новую модель, упомянутую выше, и пользовательский интерфейс создания ADF переключился на создание новой модели.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **HdfsSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |
| distcpSettings | Группа свойств при использовании HDFS DistCp. | Нет |
| resourceManagerEndpoint | Конечная точка диспетчер ресурсов Yarn | Да, если используется DistCp |
| tempScriptPath | Путь к папке для хранения временного командного скрипта DistCp. Файл скрипта создает фабрика данных, и после завершения задания копирования файл удаляется. | Да, если используется DistCp |
| distcpOptions | Дополнительные параметры для команды DistCp. | Нет |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу хранилища. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет |

**Пример: источник HDFS в действии копирования с помощью DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Дополнительные сведения о том, как использовать DistCp для эффективного копирования данных из HDFS, см. в следующем разделе.

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName             | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные **полужирным** шрифтом) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (пусто, используйте по умолчанию) | нет     | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | (пусто, используйте по умолчанию) | Да      | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | `*.csv`              | нет     | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*`  | `*.csv`              | Да      | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Использовать DistCp для копирования данных из HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) является программой командной строки Hadoop для создания распределенной копии в кластере Hadoop. При запуске команды Distcp сначала будут перечислены все файлы, которые будут скопированы, созданы несколько заданий сопоставления в кластере Hadoop, и каждое задание сопоставления будет выполнять двоичное копирование из источника в приемник.

Поддержка действий копирования с помощью DistCp для копирования файлов "как есть" в большой двоичный объект Azure (включая [промежуточное копирование](copy-activity-performance.md)) или Azure Data Lake Store, в этом случае она может полностью использовать мощность кластера, а не работать на автономных Integration Runtime. При этом пропускная способность копирования будет выше, особенно если ваш кластер очень мощный. Основываясь на вашей конфигурации в Azure Data Factory, действие копирования автоматически создает команду distcp, передает в ваш кластер Hadoop и контролирует статус копирования.

### <a name="prerequisites"></a>Предварительные требования

Чтобы использовать DistCp для копирования файлов "как есть" из HDFS в большой двоичный объект Azure (включая промежуточную копию) или в Azure Data Lake Store, убедитесь, что ваш кластер Hadoop соответствует требованиям ниже:

1. Службы MapReduce и Yarn включены.
2. Версия Yarn — 2.5 или более поздняя.
3. Сервер HDFS интегрирован с вашим целевым хранилищем данных (большой двоичный объект Azure или Azure Data Lake Store):

    - Начиная с версии Hadoop 2.7, изначально поддерживается файловая система больших двоичных объектов Azure. Необходимо указать путь к JAR-файлу в конфигурации среды Hadoop.
    - Файловая система Azure Data Lake Store упаковывается, начиная с Hadoop версии 3.0.0-alpha1. Если ваш кластер Hadoop ниже этой версии, вам нужно вручную импортировать пакеты JAR, связанные с ADLS (azure-datalake-store.jar), в кластер [здесь](https://hadoop.apache.org/releases.html) и указать путь к JAR-файлу в конфигурации среды Hadoop.

4. Подготовьте временную папку в HDFS. Она используется для хранения скрипта оболочки DistCp, поэтому она будет занимать пространство на уровне килобайтов.
5. Убедитесь, что учетная запись пользователя, предоставленная в связанной службе HDFS, имеет разрешение на отправку приложение в Yarn, создание вложенных папок, чтение и запись файлов в указанной выше временной папке.

### <a name="configurations"></a>Конфигурации

См. раздел конфигурации, связанные с DistCp, и примеры в [HDFS в качестве источника](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Использование аутентификации Kerberos для соединителя HDFS

Есть два способа настроить локальную среду для использования аутентификации Kerberos в соединителе HDFS. Вы можете применить тот, который соответствует вашему сценарию.
* Вариант 1. [Присоединение компьютера, где выполняется локальная среда выполнения интеграции к области Kerberos](#kerberos-join-realm)
* Вариант 2. [Активация взаимного доверия между доменом Windows и областью Kerberos](#kerberos-mutual-trust).

### <a name="kerberos-join-realm"></a>Вариант 1. Присоединение компьютера, где выполняется локальная среда выполнения интеграции к области Kerberos

#### <a name="requirements"></a>Требования

* Компьютер, где выполняется локальная среда выполнения интеграции, нужно присоединить к области Kerberos. Его нельзя присоединять к домену Windows.

#### <a name="how-to-configure"></a>Процесс настройки

**На компьютере, где выполняется локальная среда выполнения интеграции:**

1.  Запустите служебную программу **Ksetup** для настройки сервера центра распространения ключей и области Kerberos.

    Компьютер должен быть настроен как член рабочей группы, так как области Kerberos отличаются от доменов Windows. Для этого нужно настроить область Kerberos и добавить сервер центра распространения ключей, как описано ниже. Вместо *REALM.COM* укажите соответствующее имя области.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Перезапустите** компьютер после выполнения этих двух команд.

2.  Проверьте конфигурацию с помощью команды **Ksetup**. Результат должен выглядеть примерно так:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS на использование **аутентификации Windows**, указав имя участника Kerberos и пароль для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

### <a name="kerberos-mutual-trust"></a>Вариант 2. Активация взаимного доверия между доменом Windows и областью Kerberos

#### <a name="requirements"></a>Требования

*   Компьютер, где выполняется локальная среда выполнения интеграции, необходимо присоединить к домену Windows.
*   Вам потребуются права на изменение параметров контроллера домена.

#### <a name="how-to-configure"></a>Процесс настройки

> [!NOTE]
> Вместо REALM.COM и AD.COM в приведенном ниже примере укажите соответствующие имена области и контроллера домена.

**Настройка на сервере центра распространения ключей**

1. Измените конфигурацию центра распространения ключей в файле **krb5.conf**, чтобы создать отношения доверия с доменом Windows, как указано ниже в шаблоне конфигурации. По умолчанию эта конфигурация находится в файле **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Перезапустите** службу KDC после настройки.

2. Подготовьте участника с именем **KRBTGT/realm. COM\@AD.com** в KDC Server с помощью следующей команды:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. В файле **hadoop.security.auth_to_local** добавьте к конфигурации HDFS правило `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Настройка на контроллере домена**

1.  Выполните приведенные ниже команды **Ksetup**, чтобы добавить запись области:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Установите доверительные отношения между доменом Windows и областью Kerberos. [password] — это пароль для основного сервера **KRBTGT/realm. COM\@AD.com**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Выберите алгоритм шифрования, используемый в Kerberos.

    1. Последовательно выберите "Диспетчер серверов" > "Управление групповой политикой" > "Домен" > "Объекты групповой политики" > "Групповая политика по умолчанию или действующая групповая политика", затем нажмите "Редактировать".

    2. Откроется всплывающее окно **редактора управления групповой политикой**. Последовательно выберите "Конфигурация компьютера" > "Политики" > "Параметры Windows" > "Параметры безопасности" > "Локальные политики" > "Параметры безопасности". Затем щелкните **Сетевая безопасность: настройка типов шифрования, разрешенных Kerberos**.

    3. Выберите алгоритм шифрования, который вы хотите использовать при подключении к центру распространения ключей. Обычно можно просто выбрать все доступные параметры.

        ![Типы шифрования конфигурации для Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. С помощью команды **Ksetup** задайте алгоритм шифрования, который будет использоваться для этой области.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Создайте сопоставление между субъектом Kerberos и учетной записью домена, чтобы использовать субъект Kerberos в домене Windows.

    1. Щелкните "Средства администрирования" и выберите **Пользователи и компьютеры Active Directory**.

    2. Настройте расширенные функции, щелкнув **Просмотр** > **Расширенные функции**.

    3. Найдите учетную запись, для которой нужно создать сопоставление, и щелкните ее правой кнопкой мыши, чтобы открыть **Сопоставление имен** > **Имена Kerberos**.

    4. Добавьте субъект-службу из области.

        ![Сопоставление удостоверения безопасности](media/connector-hdfs/map-security-identity.png)

**На компьютере, где выполняется локальная среда выполнения интеграции:**

* Выполните приведенные ниже команды **Ksetup**, чтобы добавить запись области.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Настройка фабрики данных Azure**

* Настройте соединитель HDFS для использования **аутентификации Windows**, указав учетную запись домена или имя субъекта-службы Kerberos для подключения к источнику данных HDFS. Проверьте сведения о конфигурации — [свойства связанной службы HDFS](#linked-service-properties).

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
