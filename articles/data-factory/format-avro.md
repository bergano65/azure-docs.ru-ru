---
title: Формат Avro в фабрике данных Azure
description: В этом разделе описывается, как работать с форматом Avro в фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416351"
---
# <a name="avro-format-in-azure-data-factory"></a>Формат Avro в фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Если требуется **выполнить синтаксический анализ файлов Avro или записать данные в формат Avro**, следуйте этой статье. 

Формат Avro поддерживается для следующих соединителей: [Amazon S3](connector-amazon-simple-storage-service.md), [большой двоичный объект Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [хранилище файлов Azure](connector-azure-file-storage.md), [Файловая система](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Avro.

| Свойство         | Описание                                                  | Обязательный |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство Type набора данных должно иметь значение **Avro**. | Да      |
| location         | Параметры расположения файлов. Каждый файловый соединитель имеет собственный тип расположения и поддерживаемые свойства в разделе `location`. **См. сведения в статье о соединителе. раздел свойств набора данных >**. | Да      |
| аврокомпрессионкодек | Кодек сжатия, используемый при записи в файлы Avro. При чтении из файлов Avro фабрика данных автоматически определяет кодек сжатия на основе метаданных файла.<br>Поддерживаемые типы: "**нет**" (по умолчанию), "**Deflate**", "**Привязка**". Примечание. в настоящее время действие копирования не поддерживает привязку при чтении и записи файлов Avro. | Нет       |

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

В разделе *** \*источник\* *** действия копирования поддерживаются следующие свойства.

| Свойство      | Описание                                                  | Обязательный |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство Type источника действия копирования должно иметь значение **авросаурце**. | Да      |
| сторесеттингс | Группа свойств для чтения данных из хранилища данных. Каждый файловый соединитель имеет собственные Поддерживаемые параметры чтения в разделе `storeSettings`. Дополнительные **сведения см. в статье о соединителе — > свойства действия копирования**. | Нет       |

### <a name="avro-as-sink"></a>Avro в качестве приемника

В разделе *** \*приемника\* *** действия копирования поддерживаются следующие свойства.

| Свойство      | Описание                                                  | Обязательный |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство Type источника действия копирования должно иметь значение **авросинк**. | Да      |
| сторесеттингс | Группа свойств для записи данных в хранилище данных. Каждый соединитель на основе файлов имеет собственные Поддерживаемые параметры записи в `storeSettings`. Дополнительные **сведения см. в статье о соединителе — > свойства действия копирования**. | Нет       |

## <a name="data-type-support"></a>Поддержка типов данных

### <a name="copy-activity"></a>Действие копирования
[Сложные типы данных](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro не поддерживаются (записи, перечисления, массивы, карты, объединения и фиксированные) в действии копирования.

### <a name="data-flows"></a>Потоки данных
При работе с файлами Avro в потоках данных можно читать и записывать сложные типы данных, но сначала следует очистить физическую схему из набора данных. В потоках данных можно задать логическую проекцию и производные столбцы, которые являются сложными структурами, а затем отображать эти поля в файле Avro.

## <a name="next-steps"></a>Дальнейшие шаги

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
