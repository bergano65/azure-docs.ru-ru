---
title: Копирование данных из файловой системы или в нее с помощью Фабрики данных Azure
description: Узнайте, как копировать данные из файловой системы в поддерживаемые хранилища данных-приемники или в файловую систему из поддерживаемых хранилищ данных-источников с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: fd8fd7b7657622978e9019b09ba0d3d885f803fd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656460"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Копирование данных из файловой системы или обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-onprem-file-system-connector.md)
> * [Текущая версия](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается копирование данных в файловую систему и из нее. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Соединитель файловой системы предназначен для следующих действий:

- [Действие копирования](copy-activity-overview.md) с использованием матрицы [поддерживаемых источников и приемников](copy-activity-overview.md).
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Действие удаления](delete-activity.md)

В частности, этот соединитель файловой системы поддерживает:

- Копирование файлов из локального компьютера или сетевой общей папки и обратно. Чтобы использовать общую папку Linux, установите на сервер Linux [Samba](https://www.samba.org/).
- Копирование файлов с использованием проверки подлинности **Windows**.
- Копирование файлов "как есть", анализ или создание файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для файловой системы.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы файловой системы поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать значение **FileServer**. | Да |
| host | Задает корневой путь к папке, которую необходимо скопировать. Чтобы указать специальные знаки в строке, используйте escape-символ "\". Примеры приведены в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions). | Да |
| userid | Укажите идентификатор пользователя, имеющего доступ к серверу. | Да |
| password | Укажите пароль для пользователя (userid). Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |нет |

### <a name="sample-linked-service-and-dataset-definitions"></a>Примеры определений связанной службы и набора данных

| Сценарий | host в определении связанной службы | folderPath в определении набора данных |
|:--- |:--- |:--- |
| Локальная папка на компьютере среды выполнения интеграции: <br/><br/>Примеры: D:\\\* или D:\folder\subfolder\\* |В формате JSON: `D:\\`<br/>В пользовательском интерфейсе: `D:\` |В формате JSON: `.\\` или `folder\\subfolder`<br>В пользовательском интерфейсе: `.\` или `folder\subfolder` |
| Удаленная общая папка: <br/><br/>Примеры: "\\\\сервер\\общая_папка\\\\*" или "\\\\сервер\\общая_папка\\папка\\вложенная_папка\\\*" |В формате JSON: `\\\\myserver\\share`<br/>В пользовательском интерфейсе: `\\myserver\share` |В формате JSON: `.\\` или `folder\\subfolder`<br/>В пользовательском интерфейсе: `.\` или `folder\subfolder` |

>[!NOTE]
>При создании и настройке с помощью пользовательского интерфейса не нужно вводить две обратные косые черты (`\\`) в качестве escape-символа, как при использовании JSON. Укажите одну обратную косую черту.

**Пример**.

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
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

Ниже перечислены свойства, которые поддерживаются для файловой системы в параметрах `location` в наборе данных на основе формата:

| Свойство   | Описание                                                  | Обязательно |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Свойство типа в `location` для набора данных должно иметь значение **FileServerLocation**. | Да      |
| folderPath | Путь к папке. Если вы хотите использовать подстановочный знак для фильтрации папки, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |
| fileName   | Имя файла в заданном пути к папке (folderPath). Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите этот параметр и укажите его в параметрах источника действия. | нет       |

**Пример**.

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых файловой системой источника и приемника.

### <a name="file-system-as-source"></a>Файловая система в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Ниже перечислены свойства, которые поддерживаются для файловой системы в параметрах `storeSettings` в источнике копирования на основе формата.

| Свойство                 | Описание                                                  | Обязательно                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Для свойства типа в разделе `storeSettings` необходимо задать значение **FileServerReadSettings**. | Да                                           |
| ***Поиск файлов для копирования:*** |  |  |
| ВАРИАНТ 1. Статический путь<br> | Копирование из указанного пути к папке или файлу, заданному в наборе данных. Если вы хотите скопировать все файлы из папки, дополнительно укажите `wildcardFileName` со значением `*`. |  |
| ВАРИАНТ 2. Подстановочный знак<br>- wildcardFolderPath | Путь к папке с подстановочными знаками для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | нет                                            |
| ВАРИАНТ 2. Подстановочный знак<br>- wildcardFileName | Имя файла с подстановочными знаками в заданном пути folderPath или wildcardFolderPath для фильтрации исходных папок. <br>Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если фактическое имя папки содержит подстановочный знак или escape-символ.  Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да |
| ВАРИАНТ 3. Список файлов<br>- fileListPath | Указывает, что нужно скопировать заданный набор файлов. Определите текстовый файл со списком файлов, которые необходимо скопировать, по одному файлу в строке (каждая строка должна содержать относительный путь к заданному в наборе данных пути).<br/>При использовании этого варианта не указывайте имя файла в наборе данных. Ознакомьтесь с дополнительными примерами в разделе [Примеры списков файлов](#file-list-examples). |нет |
| ***Дополнительные параметры:*** |  | |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. <br>Допустимые значения: **true** (по умолчанию) и **false**.<br>Это свойство не применяется при настройке `fileListPath`. |нет |
| modifiedDatetimeStart    | Фильтр файлов на основе атрибута: последнее изменение. <br>Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL. Это означает, что фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.<br/>Это свойство не применяется при настройке `fileListPath`. | нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | нет                                            |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет                                            |

**Пример**.

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSettings",
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

### <a name="file-system-as-sink"></a>Файловая система в качестве приемника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Ниже перечислены свойства, которые поддерживаются для файловой системы в параметрах `storeSettings` для приемника копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Свойство типа в разделе `storeSettings` должно иметь значение **FileServerWriteSettings**. | Да      |
| copyBehavior             | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | нет       |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу данных. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет       |

**Пример**.

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
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

### <a name="file-list-examples"></a>Примеры списков файлов

В этом разделе описывается поведение, возникающее при указании пути к списку файлов в качестве источника для действия копирования.

Предположим, что у вас есть следующая исходная структура папок и вы хотите скопировать файлы, выделенные полужирным шрифтом:

| Пример исходной структуры                                      | Содержимое файла FileListToCopy.txt                             | Конфигурация ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Метаданные<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Файл1.csv<br>Вложенная_папка1/Файл3.csv<br>Вложенная_папка1/Файл5.csv | **В наборе данных:**<br>– путь к папке: `root/FolderA`<br><br>**В источнике действия копирования:**<br>– путь к списку файлов: `root/Metadata/FileListToCopy.txt` <br><br>Путь к списку файлов указывает на текстовый файл в том же хранилище данных, который содержит список файлов для копирования (с указанием одного файла в строке с относительным путем к пути, заданному в наборе данных). |

### <a name="recursive-and-copybehavior-examples"></a>Примеры recursive и copyBehavior

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| Да |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается с такой же структурой, как и исходная папка:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| false |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Подробные сведения об этих свойствах см. в статье [Действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия GetMetadata

Подробные сведения об этих свойствах см. в статье [Действие GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Свойства действия удаления

Подробные сведения об этих свойствах см. в статье [Действие удаления](delete-activity.md).

## <a name="legacy-models"></a>Устаревшие модели

>[!NOTE]
>Следующие модели по-прежнему поддерживаются на условиях "как есть" для обеспечения обратной совместимости. В дальнейшем рекомендуется использовать новую модель, упомянутую в разделах выше. Пользовательский интерфейс разработки ADF был изменен для создания новой модели.

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **FileShare**. |Да |
| folderPath | Путь к папке, Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактической папки содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделах [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions) и [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |нет |
| fileName | **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ.<br/><br/>Если fileName для выходного набора данных не указан, а **preserveHierarchy** не указан в приемнике действия, действие копирования автоматически создаст имя файла по следующему шаблону: "*Data.[GUID идентификатора выполнения действия].[GUID, если поведение — FlattenHierarchy].[формат, если настроен].[сжатие, если настроено]* ". Пример: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz. При копировании из табличного источника с использованием имени таблицы вместо запроса формат имени будет таким: " *[имя таблицы].[фoрмат].[сжатие, если настроено]* ". Например, "MyTable.csv". |нет |
| modifiedDatetimeStart | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учтите, что включение этого параметра в случае, если вы пытаетесь использовать фильтрацию для огромного количества файлов, повлияет на общую производительность перемещения данных. <br/><br/> Свойства могут иметь значение NULL, и в этом случае фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| modifiedDatetimeEnd | Фильтр файлов на основе атрибута: последнее изменение. Файлы будут выбраны, если время их последнего изменения находится в диапазоне времени `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время представлено часовым поясом UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Учтите, что включение этого параметра в случае, если вы пытаетесь использовать фильтрацию для огромного количества файлов, повлияет на общую производительность перемещения данных. <br/><br/> Свойства могут иметь значение NULL, и в этом случае фильтры атрибута файла не будут применяться к набору данных.  Если для параметра `modifiedDatetimeStart` задано значение даты и времени, но параметр `modifiedDatetimeEnd` имеет значение NULL, то будут выбраны файлы, чей атрибут последнего изменения больше указанного значения даты и времени или равен ему.  Если для параметра `modifiedDatetimeEnd` задано значение даты и времени, но параметр `modifiedDatetimeStart` имеет значение NULL, то будут выбраны все файлы, чей атрибут последнего изменения меньше указанного значения даты и времени.| нет |
| format | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать или создать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs-legacy.md#text-format), [формате Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Оптимальный** и **Самый быстрый**. |нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с заданным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков.

>[!NOTE]
>Если вы использовали свойство fileFilter для фильтрации файлов, оно по-прежнему поддерживается без изменений, а вам предлагается далее использовать новую возможность фильтрации, добавленную к fileName.

**Пример**.

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
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

### <a name="legacy-copy-activity-source-model"></a>Устаревшая модель источника действия копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойству type источника действия копирования необходимо задать значение **FileSystemSource**. |Да |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства recursive задано значение true, а приемником является файловое хранилище, в приемнике не будут создаваться пустые папки и вложенные папки.<br/>Допустимые значения: **true** (по умолчанию), **false**. | нет |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет |

**Пример**.

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>Устаревшая модель приемника действия копирования

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type приемника действия копирования должно иметь следующее значение: **FileSystemSink**. |Да |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из хранилища данных на основе файлов.<br/><br/>Допустимые значения:<br/><b>- PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>- FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированное имя. <br/><b>- MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Дедупликация записей во время объединения не выполняется. Если указано имя файла, объединенному файлу будет присвоено именно это имя. В противном случае имя файла создается автоматически. | нет |
| maxConcurrentConnections | Количество одновременных подключений к хранилищу. Укажите, только если требуется ограничить одновременные подключения к хранилищу данных. | нет |

**Пример**.

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
