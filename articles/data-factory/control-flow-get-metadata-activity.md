---
title: Действие получения метаданных в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как можно использовать действие with Metadata в конвейере фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 320e92e45f319e394b5a38b3f1e8ef3f314920b8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966346"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Действие получения метаданных в фабрике данных Azure

Действие GetMetadata можно использовать для получения **метаданных** для любых данных в фабрике данных Azure. Его можно использовать в следующих сценариях:

- Проверка информации о метаданных любых данных.
- Активация конвейера, когда данные готовы или доступны.

В потоке управления доступны следующие функциональные возможности:

- Выходные данные действия GetMetadata можно использовать в условных выражениях для выполнения проверки.
- Конвейер может сработать, если условие выполняется в цикле Do-Until.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Действие GetMetadata принимает набор данных в качестве необходимых входных данных и выводит сведения о метаданных в качестве выходных данных. Сейчас поддерживаются указанные ниже соединители с соответствующими извлекаемыми метаданными. Максимальный поддерживаемый размер метаданных составляет **1 МБ**.

>[!NOTE]
>При запуске действия GetMetadata в локальной среде выполнения интеграции последние функции поддерживаются в версии 3.6 или более поздней версии. 

### <a name="supported-connectors"></a>Поддерживаемые соединители

**Хранилище файлов:**

| Соединитель и метаданные | itemName<br>(файл или папка) | itemType<br>(файл или папка) | размер<br>(файл) | создано<br>(файл или папка) | lastModified<br>(файл или папка) |childItems<br>(папка) |contentMD5<br>(файл) | structure<br/>(файл) | columnCount<br>(файл) | exists (существует)<br>(файл или папка) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | п | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | п | √ | √ | √/√* |
| [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Хранилище Azure Data Lake Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | п | √ | √ | √/√ |
| [Хранилище Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | п | √ | √ | √/√ |
| [Хранилище файлов Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | п | √ | √ | √/√ |
| [Файловая система](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | п | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | п | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | п | √ | √ | √/√ |

- Для Amazon S3 и Google Cloud Storage `lastModified` применяется к контейнеру и ключу, но не к виртуальной папке; `exists` и применяется к контейнеру и ключу, но не к префиксу или виртуальной папке.
- В BLOB-объектах Azure `lastModified` используется с контейнерами и BLOB-объектами, но не с виртуальными папками.

**Реляционная база данных:**

| Соединитель и метаданные | structure | columnCount | exists (существует) |
|:--- |:--- |:--- |:--- |
| [база данных SQL Azure;](connector-azure-sql-database.md) | √ | √ | √ |
| [Управляемый экземпляр Базы данных SQL Azure](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Хранилище данных Azure SQL](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Параметры метаданных

В списке полей действия GetMetadata можно указать следующие типы метаданных для извлечения:

| Тип метаданных | Описание |
|:--- |:--- |
| itemName | Имя файла или папки. |
| itemType | Тип файла или папки. Выходное значение: `File` или `Folder`. |
| размер | Размер файла в байтах. Применяется только к файлам. |
| создано | Дата и время создания файла или папки. |
| lastModified | Дата и время последнего изменения файла или папки. |
| childItems | Список вложенных папок и файлов в указанной папке. Применяется только к папкам. Выходное значение представляет собой список с именем и типом каждого дочернего элемента. |
| contentMD5 | Сверка MD5 файла. Применяется только к файлам. |
| structure | Структура данных в файле или таблице реляционной базы данных. Выходное значение представляет собой список с именами и типами столбцов. |
| columnCount | Число столбцов в файле или реляционной таблице. |
| exists (существует)| Существует ли файл, папка или таблица. Обратите внимание, что если параметр "exists" указан в списке полей GetMetadata, действие не завершается сбоем даже в том случае, если указанный элемент (файл, папка или таблица) не существует; вместо этого в качестве выходных данных возвращается `exists: false`. |

>[!TIP]
>Чтобы проверить, существует ли файл, папка или таблица, укажите `exists` в списке полей действия GetMetadata. Затем вы сможете проверить результат `exists: true/false` в выходных данных действия. Если в списке полей не указан параметр `exists`, то в случае отсутствия объекта действие завершится сбоем.

>[!NOTE]
>При получении метаданных из файловых хранилищ и настройке `modifiedDatetimeStart` и/или `modifiedDatetimeEnd`, `childItems` в выходных данных возвращаются только файлы с заданным путем последнего изменения между диапазоном, но не вложенные папки.

## <a name="syntax"></a>Синтаксис

**Действие GetMetadata:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Набор данных:**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Свойства типа

Сейчас с помощью действия GetMetadata можно получать следующие типы сведений о метаданных.

Свойство | Описание | Обязательное значение
-------- | ----------- | --------
fieldList | Содержит типы необходимых сведений о метаданных. Сведения о поддерживаемых метаданных см. в разделе [Параметры метаданных](#metadata-options). | Да 
dataset | Эталонный набор данных, действие которого требуется получить с помощью действия GetMetadata. Сведения о поддерживаемых соединителях см. в разделе [Поддерживаемые соединители](#supported-capabilities); сведения о синтаксисе набора данных см. в разделе о соединителях. | Да
форматсеттингс | Применяется при использовании набора данных типа Format. | Нет
сторесеттингс | Применяется при использовании набора данных типа Format. | Нет

## <a name="sample-output"></a>Пример выходных данных

Результат действия GetMetadata отображается в выходных данных действия. Ниже приведены два примера с исчерпывающим набором параметров метаданных, указанных в списке полей. Чтобы использовать этот результат в последующем действии, примените шаблон `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Получение метаданных файла

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Получение метаданных папки

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Следующие шаги
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
