---
title: Получите активность Метаданных на фабрике данных Azure
description: Узнайте, как использовать действие Get Metadata в конвейере Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: a0c07aaf27825254f776a03b9b9ca2cbeddca02d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250271"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Получите активность Метаданных на фабрике данных Azure

Для получения метаданных на фабрике данных Azure можно использовать действие Get Metadata. Вы можете использовать это действие в следующих сценариях:

- Проверка метаданных любых данных.
- Триггер конвейера, когда данные готовы/доступны.

В потоке управления доступны следующие функциональные возможности:

- Для выполнения проверки можно использовать выход из действия Get Metadata в условных выражениях.
- Вы можете запустить конвейер, когда условие будет выполнено с помощью Do Until looping.

## <a name="capabilities"></a>Возможности

Активность Get Metadata использует набор данных в качестве ввода и возвращает информацию о метаданных в виде вывода. В настоящее время поддерживаются следующие разъемы и соответствующие извлекаемые метаданные. Максимальный размер возвращенных метаданных составляет 2 МБ.

>[!NOTE]
>Если вы выполняете действие Get Metadata в автономном времени выполнения интеграции, последние возможности поддерживаются в версии 3.6 или позже.

### <a name="supported-connectors"></a>Поддерживаемые соединители

**Хранилище файлов**

| Соединитель и метаданные | itemName<br>(файл или папка) | itemType<br>(файл или папка) | size<br>(файл) | created<br>(файл или папка) | lastModified<br>(файл или папка) |childItems<br>(папка) |contentMD5<br>(файл) | structure<br/>(файл) | columnCount<br>(файл) | exists (существует)<br>(файл или папка) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Хранение Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Хранилище Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Лазурное хранилище озер данных Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Файлы Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Файловая система](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Для Amazon S3 и `lastModified` Google Cloud Storage применяется к веру и `exists` ключу, но не к виртуальной папке, и относится к ведре и ключу, но не к приставке или виртуальной папке.
- Для хранения Azure `lastModified` Blob применяется к контейнеру и каплей, но не к виртуальной папке.
- `lastModified`фильтр в настоящее время применяется для фильтрации элементов ребенка, но не указанная папка / файл себя.
- Фильтр Wildcard на папках/файлах не поддерживается для получения активности Metadata.

**Реляционная база данных**

| Соединитель и метаданные | structure | columnCount | exists (существует) |
|:--- |:--- |:--- |:--- |
| [База данных SQL Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Управляемый экземпляр базы данных Azure S'L](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Параметры метаданных

Можно указать следующие типы метаданных в списке полей активности Get Metadata для получения соответствующей информации:

| Тип метаданных | Описание |
|:--- |:--- |
| itemName | Имя файла или папки. |
| itemType | Тип файла или папки. Возвратное `File` `Folder`значение или . |
| size | Размер файла, байты. Применяется только к файлам. |
| created | Дата и время создания файла или папки. |
| lastModified | Дата и время последнего изменения файла или папки. |
| childItems | Список подфолок и файлов в данной папке. Применяется только к папкам. Возвратное значение — это список имени и типа каждого элемента «ребенок». |
| contentMD5 | Сверка MD5 файла. Применяется только к файлам. |
| structure | Структура данных файла или реляционной таблицы базы данных. Возвратное значение — это список имен столбцов и типов столбцов. |
| columnCount | Количество столбцов в файле или реляционной таблице. |
| exists (существует)| Существует ли файл, папка или таблица. Обратите внимание, что если `exists` в списке полеget Get Metadata указан список Get Metadata, действие не будет сбой, даже если файл, папка или таблица не существует. Вместо `exists: false` этого возвращается в вывод. |

>[!TIP]
>Если требуется проверить существование файла, папки `exists` или таблицы, укажите в списке полей действия Get Metadata. Затем можно проверить `exists: true/false` результат вывода активности. Если `exists` в списке поле не указан, действие Get Metadata выйдет из строя, если объект не найден.

>[!NOTE]
>Когда вы получаете метаданные из `modifiedDatetimeStart` `modifiedDatetimeEnd`файловых `childItems` хранилиров и настраиваете или, в выводе будут включены только файлы в заданном пути, которые имеют последнее измененное время в пределах указанного диапазона. В не включайте элементы в субфолила.

## <a name="syntax"></a>Синтаксис

**Получить активность метаданных**

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

**Набор данных**

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

В настоящее время деятельность Get Metadata может возвращать следующие типы информации о метаданных:

Свойство | Описание | Обязательно
-------- | ----------- | --------
fieldList | Требуются типы информации о метаданных. Для получения подробной информации об поддерживаемых метаданных смотрите раздел [вариантов метаданных](#metadata-options) в этой статье. | Да 
набор данных | Набор справочных данных, метаданные которого должны быть извлечены в ходе действия Get Metadata. См в разделе [Возможности](#capabilities) можно узнать о поддерживаемых разъемах. Обратитесь к конкретным темам разъема для деталей синтаксиса. | Да
форматНастройкы | При использовании набора данных типа формата. | нет
storeSettings | При использовании набора данных типа формата. | нет

## <a name="sample-output"></a>Пример выходных данных

Результаты Получения Метаданных отображаются на выходе активности. Ниже приведены два примера, показывающие обширные параметры метаданных. Чтобы использовать результаты в последующем действии, используйте этот шаблон: `@{activity('MyGetMetadataActivity').output.itemName}`.

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

## <a name="next-steps"></a>Дальнейшие действия
Узнайте о других действиях, поддерживаемых Data Factory:

- [Выполнение деятельности трубопровода](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
