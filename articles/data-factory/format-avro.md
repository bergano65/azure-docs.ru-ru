---
title: Формат Avro в фабрике данных Azure
description: В этом разделе описывается, как работать с форматом Avro в фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927415"
---
# <a name="avro-format-in-azure-data-factory"></a>Формат Avro в фабрике данных Azure

Если требуется **выполнить синтаксический анализ файлов Avro или записать данные в формат Avro**, следуйте этой статье. 

Формат Avro поддерживается для следующих соединителей: [Amazon S3](connector-amazon-simple-storage-service.md), [большой двоичный объект Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [хранилище файлов Azure](connector-azure-file-storage.md), [Файловая система](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Avro.

| Свойство         | Описание                                                  | Обязательно для заполнения |
| ---------------- | ------------------------------------------------------------ | -------- |
| Тип             | Свойство Type набора данных должно иметь значение **Avro**. | ДА      |
| location         | Параметры расположения файлов. Каждый файловый соединитель имеет собственный тип расположения и поддерживаемые свойства в разделе `location`. **См. сведения в статье о соединителе. раздел свойств набора данных >** . | ДА      |
| аврокомпрессионкодек | Кодек сжатия, используемый при записи в файлы Avro. При чтении из файлов Avro фабрика данных автоматически определяет кодек сжатия на основе метаданных файла.<br>Поддерживаемые типы: "**нет**" (по умолчанию), "**Deflate**", "**Привязка**". | Нет       |

> [!NOTE]
> Пробелы в имени столбца не поддерживаются для файлов Avro.

Ниже приведен пример набора данных Avro в хранилище BLOB-объектов Azure.

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником Avro.

### <a name="avro-as-source"></a>Avro в качестве источника

Следующие свойства поддерживаются в разделе действие копирования ***\*источник\**** .

| Свойство      | Описание                                                  | Обязательно для заполнения |
| ------------- | ------------------------------------------------------------ | -------- |
| Тип          | Свойство Type источника действия копирования должно иметь значение **авросаурце**. | ДА      |
| сторесеттингс | Группа свойств для чтения данных из хранилища данных. Каждый файловый соединитель имеет собственные Поддерживаемые параметры чтения в разделе `storeSettings`. Дополнительные **сведения см. в статье о соединителе — > свойства действия копирования**. | Нет       |

### <a name="avro-as-sink"></a>Avro в качестве приемника

В разделе\*действия копирования ***\*приемника*** поддерживаются следующие свойства.

| Свойство      | Описание                                                  | Обязательно для заполнения |
| ------------- | ------------------------------------------------------------ | -------- |
| Тип          | Свойство Type источника действия копирования должно иметь значение **авросинк**. | ДА      |
| сторесеттингс | Группа свойств для записи данных в хранилище данных. Каждый соединитель на основе файлов имеет собственные Поддерживаемые параметры записи в разделе `storeSettings`. Дополнительные **сведения см. в статье о соединителе — > свойства действия копирования**. | Нет       |

## <a name="data-type-support"></a>Поддержка типов данных

[Сложные типы данных](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro не поддерживаются (записи, перечисления, массивы, сопоставления, объединения и фиксированные).

## <a name="next-steps"></a>Дальнейшие действия

- [Действие копирования в фабрике данных Azure](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
