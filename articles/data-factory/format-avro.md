---
title: Формат Avro на фабрике данных Azure
description: В этой теме описывается, как бороться с форматом Avro на фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416351"
---
# <a name="avro-format-in-azure-data-factory"></a>Формат Avro на фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Следуйте этой статье, когда вы хотите **разобрать файлы Avro или написать данные в формате Avro**. 

Формат Avro поддерживается для следующих разъемов: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе приводится список свойств, поддерживаемых набором данных Avro.

| Свойство         | Описание                                                  | Обязательно |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство типа набора данных должно быть установлено на **Avro.** | Да      |
| location         | Настройки расположения файла (ы). Каждый файл на основе разъема имеет `location`свой собственный тип местоположения и поддерживается свойства под . **Подробности смотрите в разделе разъем -> свойства dataset**. | Да      |
| avroCompressionCodec | Кодек сжатия для использования при записи в файлы Avro. При чтении файлов Avro Data Factory автоматически определяет кодек сжатия на основе метаданных файлов.<br>Поддерживаемые типы являются "**ни один**" (по умолчанию), "**сдуть**", "**быстро**". Примечание в настоящее время Копирование деятельности не поддерживает Snappy при чтении / записи avro файлов. | нет       |

> [!NOTE]
> Белое пространство в названии столбца не поддерживается для файлов Avro.

Ниже приведен пример набора данных Avro по хранению Azure Blob:

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником avro и раковиной.

### <a name="avro-as-source"></a>Avro как источник

Следующие свойства поддерживаются в разделе *** \*источника активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **AvroSource.** | Да      |
| storeSettings | Группа свойств о том, как считывать данные из хранилища данных. Каждый файл на основе разъема `storeSettings`имеет свои собственные поддерживаемые настройки чтения под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

### <a name="avro-as-sink"></a>Avro как раковина

Следующие свойства поддерживаются в разделе *** \*раковины активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **AvroSink.** | Да      |
| storeSettings | Группа свойств о том, как записывать данные в хранилище данных. Каждый разъем на основе файлов имеет `storeSettings`свои собственные поддерживаемые настройки записи под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

## <a name="data-type-support"></a>Поддержка типов данных

### <a name="copy-activity"></a>Действие копирования
В copy Activity не поддерживаются [сложные типы данных](https://avro.apache.org/docs/current/spec.html#schema_complex) Avro (записи, цифры, массивы, карты, союзы и фиксированные) в copy Activity.

### <a name="data-flows"></a>Потоки данных
При работе с файлами Avro в потоках данных можно читать и писать сложные типы данных, но не забудьте сначала очистить физическую схему от набора данных. В потоках данных можно настроить логическую проекцию и получить столбцы, которые являются сложными структурами, а затем автоматически сопоставить эти поля с файлом Avro.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
