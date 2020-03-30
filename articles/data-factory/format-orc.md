---
title: Формат ORC на фабрике данных Azure
description: В этой теме описывается, как бороться с форматом ORC на фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597496"
---
# <a name="orc-format-in-azure-data-factory"></a>Формат ORC на фабрике данных Azure

Следуйте этой статье, когда вы хотите **разобрать файлы ORC или записать данные в формате ORC.** 

Формат ORC поддерживается для следующих разъемов: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе приводится список свойств, поддерживаемых набором данных ORC.

| Свойство         | Описание                                                  | Обязательно |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство типа набора данных должно быть установлено **на Orc.** | Да      |
| location         | Настройки расположения файла (ы). Каждый файл на основе разъема имеет `location`свой собственный тип местоположения и поддерживается свойства под . **Подробности смотрите в разделе разъем -> свойства dataset**. | Да      |

Ниже приведен пример набора данных ORC по хранению Azure Blob:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Обратите внимание на следующие моменты.

* Данные сложных типов (STRUCT, MAP, LIST, UNION) не поддерживаются.
* Пробелы в именах столбцов не поддерживаются.
* Для ORC-файлов используется три [параметра сжатия](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB и SNAPPY. Фабрика данных поддерживает чтение данных из ORC-файла в любом из этих форматов. Для чтения данных используется кодек сжатия из метаданных. Однако при записи в ORC-файл фабрика данных по умолчанию выбирает ZLIB. В настоящее время изменить это поведение нельзя.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником orC и раковиной.

### <a name="orc-as-source"></a>ORC в качестве источника

Следующие свойства поддерживаются в разделе *** \*источника активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено **на OrcSource.** | Да      |
| storeSettings | Группа свойств о том, как считывать данные из хранилища данных. Каждый файл на основе разъема `storeSettings`имеет свои собственные поддерживаемые настройки чтения под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

### <a name="orc-as-sink"></a>ORC как раковина

Следующие свойства поддерживаются в разделе *** \*раковины активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **OrcSink.** | Да      |
| storeSettings | Группа свойств о том, как записывать данные в хранилище данных. Каждый разъем на основе файлов имеет `storeSettings`свои собственные поддерживаемые настройки записи под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

## <a name="using-self-hosted-integration-runtime"></a>Использование автономной интеграции Runtime

> [!IMPORTANT]
> Для копирования, например, между магазинами данных на территории и облачными хранилищами данных, необходимо установить **64-битный JRE 8 (Java Runtime Environment) или OpenJDK** и **Microsoft Visual C'2010 Redistributable Package** на ваш ИК-машина. **as-is** Проверьте следующий абзац с более подробной информацией.

Для копирования, работаемого на самостоятельно размещенном ИК с сериализацией/десериализацией *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* файлов ORC, ADF находит время *`JAVA_HOME`* выполнения Java, сначала проверяя реестр для JRE, если не найден, во-вторых, проверяя переменную системы OpenJDK.

- **Для использования JRE**: 64-разрядный ИК требует 64-битного JRE. Ее можно найти [здесь](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Для использования OpenJDK**: Он поддерживается с ИК-версии 3.13. Упакуйте jvm.dll со всеми другими необходимыми сборками OpenJDK на компьютере с локальной IR и соответственно установите системную переменную среды JAVA_HOME.
- **Для установки Визуального пакета Redistributable Visual 2010**: Visual C'2010 Redistributable Package не устанавливается с автономными ИК-установками. Ее можно найти [здесь](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Если вы копируете данные в /из формата ORC с помощью самохозня Интеграция Runtime и нажмите ошибка говоря: "Ошибка произошла `_JAVA_OPTIONS` при ссылаясь java, сообщение: **java.lang.OutMemoryError:Java кучу пространства**", вы можете добавить переменную среды в машине, которая размещает Самостоятельно размещены ИК для настройки мин / макс кучи размер для JVM, чтобы расширить возможности такой копии, а затем перезапустить конвейер.

![Установка размера кучи виртуальной машины Java на локальной среде выполнения интеграции](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Пример: установите переменную `_JAVA_OPTIONS` со значением `-Xms256m -Xmx16g`. Флаг `Xms` указывает начальный пул выделения памяти для виртуальной машины Java (JVM), а `Xmx` указывает максимальный пул выделения памяти. Это означает, что JVM будет запущена с объемом памяти `Xms` и сможет использовать не более `Xmx` объема памяти. По умолчанию ADF использует мин 64 МБ и max 1G.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
