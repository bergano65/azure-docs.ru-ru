---
title: Копирование данных с SFTP-сервера с помощью фабрики данных Azure | Документация Майкрософт
description: Дополнительные сведения о соединителе MySQL в фабрике данных Azure, который позволяет скопировать данные с SFTP-сервера в хранилище данных, поддерживаемое в качестве приемника.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 074fb498ee5bc07bba69df04bd56bd3393e95f1b
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089424"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Копирование данных с SFTP-сервера с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-sftp-connector.md)
> * [Текущая версия](connector-sftp.md)

В этой статье описано, как копировать данные с SFTP Server. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель SFTP поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Удалить действие](delete-activity.md)

В частности, этот соединитель SFTP поддерживает:

- Копирование файлов с использованием **базовой** проверки подлинности или проверки подлинности **SshPublicKey**.
- Копирование файлов "как есть" или анализ файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к SFTP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SFTP поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать значение **Sftp**. |Да |
| host | Имя или IP-адрес SFTP-сервера. |Да |
| port | Порт, прослушиваемый SFTP-сервером.<br/>Допустимые значения: целые числа. Значение по умолчанию: **22**. |Нет |
| skipHostKeyValidation | Указывает, нужно ли пропустить проверку ключа узла.<br/>Допустимые значения: **true**, **false** (по умолчанию).  | Нет |
| hostKeyFingerprint | Содержит отпечаток ключа узла. | Да, если skipHostKeyValidation имеет значение false.  |
| authenticationType | Укажите тип аутентификации.<br/>Допустимые значения: **Basic**, **SshPublicKey**. Описание свойств и примеры JSON для каждого типа см. ниже в разделах [использование обычной аутентификации](#using-basic-authentication) и [использование аутентификации с открытым ключом SSH](#using-ssh-public-key-authentication) соответственно. |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

### <a name="using-basic-authentication"></a>Использование обычной аутентификации

Чтобы использовать базовую проверку подлинности, задайте значение **Базовый**, а также все следующие свойства в дополнение к универсальным свойствам соединителя SFTP, которые описаны в последнем разделе.

| Свойство | Описание | Обязательное значение |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Да |
| password | Пароль пользователя (userName). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |

**Пример.**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Использование аутентификации с открытым ключом SSH

Чтобы использовать проверку подлинности с открытым ключом SSH, задайте для свойства authenticationType значение **SshPublicKey**, а также все следующие свойства в дополнение к универсальным свойствам соединителя SFTP, которые описаны в последнем разделе.

| Свойство | Описание | Обязательное значение |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Да |
| privateKeyPath | Укажите доступный для среды выполнения интеграции абсолютный путь к файлу закрытого ключа. Применяется, только если в connectVia задан тип среды выполнения интеграции. | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`.  |
| privateKeyContent | Содержимое закрытого ключа SSH в кодировке Base64. Закрытый ключ SSH должен быть в формате OpenSSH. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`. |
| passPhrase | Укажите пароль или парольную фразу для расшифровки закрытого ключа, если они используются для защиты файлы ключа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да, если файл закрытого ключа защищен парольной фразой. |

> [!NOTE]
> Соединитель SFTP поддерживает только ключ OpenSSH RSA/DSA. Содержимое файла ключа должно начинаться с текста "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Если файл закрытого ключа имеет формат PPK, используйте средство Putty для преобразования из формата PPK в OpenSSH. 

**Пример 1. Проверка подлинности SshPublicKey с закрытым ключом SSH, для которого указан путь к файлу**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Аутентификация SshPublicKey с закрытым ключом SSH, для которого указано содержимое**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

- Для **Parquet, текстовых форматов, JSON, Avro и двоичного**формата, см. раздел [Parquet, текстовый, JSON, Avro и двоичный формат набора данных](#format-based-dataset) .
- Другие форматы, например **Формат ORC**, см. в разделе [другой формат набора данных](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, текстовый набор данных с разделителями, JSON, Avro и двоичный формат

Чтобы скопировать данные в **Parquet, разделенный текст, JSON, Avro и двоичный формат**, см. статью [Формат Parquet](format-parquet.md), [текстовый формат с разделителями](format-delimited-text.md), [Формат Avro](format-avro.md) и [двоичный формат](format-binary.md) в наборе данных на основе формата и поддерживаемых параметрах. . Следующие свойства поддерживаются для SFTP в `location` параметрах в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно для заполнения |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство `location` Type в наборе данных должно иметь значение **сфтплокатион**. | Да      |
| folderPath | Путь к папке. Если вы хотите использовать подстановочный знак для фильтрации папки, пропустите этот параметр и укажите в параметрах источника действия. | Нет       |
| fileName   | Имя файла в заданной folderPath. Если вы хотите использовать подстановочные знаки для фильтрации файлов, пропустите этот параметр и укажите в параметрах источника действия. | Нет       |

> [!NOTE]
> Набор **данных типа "** общая папка" с форматом Parquet/Text, упоминаемым в следующем разделе, по-прежнему поддерживается "как есть" для действия копирования, поиска и работы с метаданными для обратной совместимости. Рекомендуется использовать эту новую модель для перемотки, и пользовательский интерфейс создания ADF переключился на создание этих новых типов.

**Пример.**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

### <a name="other-format-dataset"></a>Другой формат набора данных

Чтобы скопировать данные из SFTP в **формате ORC**, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **FileShare**. |Да |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактического файла содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Да |
| fileName |  **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования знаков, если фактическое имя папки содержит подстановочный знак или этот escape-символ. |Нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учитывайте общую производительность перемещения данных, включив этот параметр, если требуется использовать фильтр файлов из огромных объемов файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учитывайте общую производительность перемещения данных, включив этот параметр, если требуется использовать фильтр файлов из огромных объемов файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| Нет |
| format | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

>[!NOTE]
>Если вы использовали свойство fileFilter для фильтрации файлов, оно по-прежнему поддерживается без изменений, а вам предлагается далее использовать новую возможность фильтрации, добавленную к fileName.

**Пример.**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SFTP.

### <a name="sftp-as-source"></a>SFTP в качестве источника

- Чтобы скопировать данные из **Parquet, разделенного текста, JSON, Avro и двоичного**формата, обратитесь к разделу [Parquet, тексту с разделителями, JSON, Avro и источнику двоичного формата](#format-based-source) .
- Чтобы выполнить копирование из других форматов, таких как **Формат ORC**, обратитесь к другому разделу [исходного формата](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, текст с разделителями, JSON, Avro и источник двоичного формата

Чтобы скопировать данные из **Parquet, разделенного текста, JSON, Avro и двоичного**формата, см. статью [Формат Parquet](format-parquet.md), [текстовый формат с разделителями](format-delimited-text.md), [Формат Avro](format-avro.md) и [двоичный формат](format-binary.md) для источника действия копирования на основе формата и поддерживается. Параметры. Следующие свойства поддерживаются для SFTP в `storeSettings` параметрах в источнике копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно для заполнения                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство Type в разделе `storeSettings` должно иметь значение **сфтпреадсеттинг**. | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет                                            |
| вилдкардфолдерпас       | Путь к папке с подстановочными знаками для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Нет                                            |
| вилдкардфиленаме         | Имя файла с подстановочными знаками в заданной folderPath/Вилдкардфолдерпас для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, `fileName` если не указано в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | Нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | Нет                                            |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу хранилища. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет                                            |

> [!NOTE]
> Для формата текста Parquet/с разделителями **FileSystemSource** источник действия копирования типа, упомянутый в следующем разделе, по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать эту новую модель для перемотки, и пользовательский интерфейс создания ADF переключился на создание этих новых типов.

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSetting",
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

#### <a name="other-format-source"></a>Другой источник формата

Чтобы скопировать данные из SFTP в **формате ORC**, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь следующее значение: **FileSystemSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | Нет |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу хранилища. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
| `Folder*` | (пусто, используйте по умолчанию) | true | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | true | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="delete-activity-properties"></a>Свойства действия удаления

Чтобы получить сведения о свойствах, проверьте [действие Удалить](delete-activity.md) .

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
