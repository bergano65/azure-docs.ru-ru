---
title: Формат паркета на фабрике данных Azure
description: В этой теме описывается, как бороться с форматом Паркета на фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597632"
---
# <a name="parquet-format-in-azure-data-factory"></a>Формат паркета на фабрике данных Azure

Следуйте этой статье, когда вы хотите **разобрать файлы паркет или написать данные в формате паркет**. 

Формат паркета поддерживается для следующих разъемов: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе приводится список свойств, поддерживаемых набором данных Паркета.

| Свойство         | Описание                                                  | Обязательно |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство типа набора данных должно быть установлено на **паркет.** | Да      |
| location         | Настройки расположения файла (ы). Каждый файл на основе разъема имеет `location`свой собственный тип местоположения и поддерживается свойства под . **Подробности смотрите в разделе разъем -> свойства dataset**. | Да      |
| компрессионныйкод | Кодек сжатия для использования при написании файлов Паркета. При чтении файлов Паркета фабрики данных автоматически определяют кодек сжатия на основе метаданных файлов.<br>Поддерживаемые типы «**никакие**«,**gzip**«,**snappy**« (по умолчанию), и «**lzo**«. Примечание в настоящее время Копирование деятельности не поддерживает L'O при чтении / записи файлы паркета. | нет       |

> [!NOTE]
> Белое пространство в названии столбца не поддерживается для файлов Parquet.

Ниже приведен пример набора данных Паркета на Хранилище Azure Blob:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником и раковиной паркета.

### <a name="parquet-as-source"></a>Паркет как источник

Следующие свойства поддерживаются в разделе *** \*источника активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **ParquetSource.** | Да      |
| storeSettings | Группа свойств о том, как считывать данные из хранилища данных. Каждый файл на основе разъема `storeSettings`имеет свои собственные поддерживаемые настройки чтения под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

### <a name="parquet-as-sink"></a>Паркет как раковина

Следующие свойства поддерживаются в разделе *** \*раковины активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **ParquetSink.** | Да      |
| storeSettings | Группа свойств о том, как записывать данные в хранилище данных. Каждый разъем на основе файлов имеет `storeSettings`свои собственные поддерживаемые настройки записи под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

Узнайте подробности из [преобразования источника](data-flow-source.md) и [преобразования раковины](data-flow-sink.md) в потоке данных отображения.

## <a name="data-type-support"></a>Поддержка типов данных

Типы паркетных комплексов данных в настоящее время не поддерживаются (например, MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Использование автономной интеграции Runtime

> [!IMPORTANT]
> Для копирования, например, между магазинами данных на территории и облачными хранилищами, необходимо **as-is**установить **64-битный JRE 8 (Java Runtime Environment) или OpenJDK** и **Microsoft Visual C'2010 Redistributable Package** на вашей ИК-машине. Проверьте следующий абзац с более подробной информацией.

Для копирования, работаемого на Самостоятельно размещенном IR с сериализацией файла Parquet/deserialization, ADF находит время выполнения Java, во-первых проверяя реестр *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* для JRE, если не найден, во-вторых, проверяя переменную *`JAVA_HOME`* системы OpenJDK.

- **Для использования JRE**: 64-разрядный ИК требует 64-битного JRE. Ее можно найти [здесь](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Для использования OpenJDK**: Он поддерживается с ИК-версии 3.13. Упакуйте jvm.dll со всеми другими необходимыми сборками OpenJDK на компьютере с локальной IR и соответственно установите системную переменную среды JAVA_HOME.
- **Для установки Визуального пакета Redistributable Visual 2010**: Visual C'2010 Redistributable Package не устанавливается с автономными ИК-установками. Ее можно найти [здесь](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Если вы копируете данные в формат Parquet или из формата Parquet с помощью локальной среди выполнения интеграции и возникает ошибка: "Ошибка при вызове Java, сообщение: **java.lang.OutOfMemoryError:Java heap space**", можно добавить переменную среды `_JAVA_OPTIONS` в компьютере, на котором размещена локальная среда выполнения интеграции для настройки минимального и максимального размера кучи для виртуальной машины Java, чтобы расширить возможности такой копии, а затем повторно запустить конвейер.

![Установка размера кучи виртуальной машины Java на локальной среде выполнения интеграции](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Пример: установите переменную `_JAVA_OPTIONS` со значением `-Xms256m -Xmx16g`. Флаг `Xms` указывает начальный пул выделения памяти для виртуальной машины Java (JVM), а `Xmx` указывает максимальный пул выделения памяти. Это означает, что JVM будет запущена с объемом памяти `Xms` и сможет использовать не более `Xmx` объема памяти. По умолчанию ADF использует мин 64 МБ и max 1G.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
