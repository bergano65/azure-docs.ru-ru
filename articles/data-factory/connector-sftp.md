---
title: Копирование данных с сервера SFTP и на
description: Узнайте о том, как копировать данные с сервера SFTP и к ней с помощью Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: 06428d4a9c4a4178212d16d42b8b3adffb5c9718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250285"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Копирование данных с сервера SFTP и на ftP с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-sftp-connector.md)
> * [Текущая версия](connector-sftp.md)

В этой статье излагается, как копировать данные с сервера SFTP и на нее. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем SFTP поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Удаление действия](delete-activity.md)

В частности, этот соединитель SFTP поддерживает:

- Копирование файлов от/к SFTP с помощью **базовой** или **SshPublicKey** аутентификации.
- Копирование файлов как есть или разбор/генерация файлов с [поддерживаемыми форматами файлов и кодеками сжатия.](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к SFTP.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SFTP поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Sftp**. |Да |
| host | Имя или IP-адрес SFTP-сервера. |Да |
| порт | Порт, прослушиваемый SFTP-сервером.<br/>Допустимые значения: целые числа. Значение по умолчанию: **22**. |нет |
| skipHostKeyValidation | Указывает, нужно ли пропустить проверку ключа узла.<br/>Допустимые значения: **true**, **false** (по умолчанию).  | нет |
| hostKeyFingerprint | Содержит отпечаток ключа узла. | Да, если skipHostKeyValidation имеет значение false.  |
| authenticationType | Укажите тип аутентификации.<br/>Допустимые значения: **Базовый**, **SshPublicKey**. Обратитесь к [использованию базовой аутентификации](#using-basic-authentication) и использованию разделов [общедоступной аутентификации ключей SSH](#using-ssh-public-key-authentication) на большем количестве свойств и образцов JSON соответственно. |Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

### <a name="using-basic-authentication"></a>Использование обычной аутентификации

Чтобы использовать базовую проверку подлинности, задайте значение **Базовый**, а также все следующие свойства в дополнение к универсальным свойствам соединителя SFTP, которые описаны в последнем разделе.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Да |
| password | Пароль пользователя (userName). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |

**Примере:**

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

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| userName | Пользователь, имеющий доступ к SFTP-серверу. |Да |
| privateKeyPath | Укажите доступный для среды выполнения интеграции абсолютный путь к файлу закрытого ключа. Применяется, только если в connectVia задан тип среды выполнения интеграции. | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`.  |
| privateKeyContent | Содержимое закрытого ключа SSH в кодировке Base64. Закрытый ключ SSH должен быть в формате OpenSSH. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Должно быть указано одно из свойств: `privateKeyPath` или `privateKeyContent`. |
| passPhrase | Укажите пароль или парольную фразу для расшифровки закрытого ключа, если они используются для защиты файлы ключа. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да, если файл закрытого ключа защищен парольной фразой. |

> [!NOTE]
> Соединитель SFTP поддерживает только ключ OpenSSH RSA/DSA. Содержимое файла ключа должно начинаться с текста "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Если файл закрытого ключа имеет формат PPK, используйте средство Putty для преобразования из формата PPK в OpenSSH. 

**Пример 1. Проверка подлинности с закрытым ключом SSH, для которого указан путь к файлу**

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

**Пример 2. Проверка подлинности с закрытым ключом SSH, для которого указано содержимое**

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `location` SFTP в настройках в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа, под `location` данными, должно быть установлено на **SftpLocation.** | Да      |
| folderPath | Путь к папке. Если требуется использовать подстановочный знак для фильтрации папки, пропустите эту настройку и укажите в настройках источника активности. | нет       |
| fileName   | Имя файла под данной папкойPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите эту настройку и укажите в настройках источника активности. | нет       |

**Примере:**

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

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SFTP.

### <a name="sftp-as-source"></a>SFTP в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `storeSettings` SFTP в настройках в формате на основе источника копий:

| Свойство                 | Описание                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **SftpReadSettings.** | Да                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Разрешенные значения **верны** (по умолчанию) и **ложные.** | нет                                            |
| подстановочный знакFolderPath       | Путь папки с символами подстановочных знаков для фильтрации папок источника. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| подстановочный знакFileName         | Имя файла с символами подстановочных знаков под данной папкойPath/wildcardFolderPath для фильтрации исходных файлов. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, `fileName` если не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                            |
| maxConcurrentConnections | Одновременное число подключений для подключения к хранилищу. Указать только при ограничении одновременного подключения к хранилику данных. | нет                                            |

**Примере:**

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP как раковина

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для `storeSettings` SFTP в настройках в формате на основе копий раковины:

| Свойство                 | Описание                                                  | Обязательно |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Свойство типа `storeSettings` под должны быть установлены на **SftpWriteSettings.** | Да      |
| copyBehavior             | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет       |
| maxConcurrentConnections | Одновременное количество подключений к хранилику данных. Указать только при ограничении одновременного подключения к хранилику данных. | нет       |
| использованиеTempFileRename | Укажите, следует ли загружать в временный файл (ы) и переименовывать, или непосредственно записывать в целевой папке / местоположение файла. По умолчанию ADF сначала записывает временный файл (ы), затем переименовывает файл при завершении загрузки, чтобы 1) избежать конфликта написать в результате поврежденного файла, если у вас есть другой процесс написания в тот же файл, и 2) обеспечить оригинальную версию файла существует во время весь перевод. Если ваш сервер SFTP не поддерживает операцию переименования, отключите эту опцию и убедитесь, что у вас нет одновременной записи в целевой файл. Смотрите наконечник устранения неполадок под этой таблицей. | Нет. Значение по умолчанию — true. |
| операцияTimeout | Время ожидания, прежде чем каждый запрос назапись на сервер SFTP раз раз. Значение по умолчанию составляет 60 мин (01:00:00).|нет |

>[!TIP]
>Если вы нажмете ошибка "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" или "SftpOperationFail" при написании данных в SFTP, и пользователь SFTP вы используете имеет надлежащее разрешение, проверьте, если ваш sFTP сервер агенство службы поддержки файла переименования - если нет, отключить "Загрузить с темпфайл" (`useTempFileRename`) вариант и попробуйте еще раз. Узнайте больше об этом свойстве сверху таблицы. Если вы используете для копирования self-hosted Integration Runtime, убедитесь, что вы используете версию 4.6 или выше.

**Примере:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName | recursive | Структура папки исхода и результат фильтра (файлы **жирным шрифтом** извлекаются)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (пусто, используйте по умолчанию) | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | (пусто, используйте по умолчанию) | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

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
| type | Свойство type для набора данных должно иметь значение **FileShare**. |Да |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактического файла содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Да |
| fileName |  **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования знаков, если фактическое имя папки содержит подстановочный знак или этот escape-символ. |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: Последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Имейте в виду, общая производительность движения данных будет влияние, позволяя эту настройку, когда вы хотите сделать файл фильтр из огромного количества файлов. <br/><br/> Свойства могут быть NULL, что означает, что фильтр атрибутов файла не будет применен к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если вы хотите **копировать файлы как между** файлохранилищами (двоичная копия), пропустите раздел формата как в водах, так и в определениях набора выходных данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs-legacy.md#text-format), [формате Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **ГЗип,** **Дефлировать**, **B'ip2**, и **цифферлат**.<br/>Поддерживаемые уровни: **Оптимальный** и **Быстрый**. |нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

>[!NOTE]
>Если вы использовали свойство fileFilter для фильтрации файлов, оно по-прежнему поддерживается без изменений, а вам предлагается далее использовать новую возможность фильтрации, добавленную к fileName.

**Примере:**

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

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
