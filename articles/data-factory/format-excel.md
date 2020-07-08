---
title: Формат Excel в фабрике данных Azure
description: В этом разделе описывается, как работать с форматом Excel в фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670847"
---
# <a name="excel-format-in-azure-data-factory"></a>Формат Excel в фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Если вы хотите **проанализировать файлы Excel**, следуйте этой статье. Фабрика данных Azure поддерживает как "xls", так и xlsx.

Формат Excel поддерживается для следующих соединителей: [Amazon S3](connector-amazon-simple-storage-service.md), [большой двоичный объект Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md), [хранилище файлов Azure](connector-azure-file-storage.md), [Файловая система](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)и [SFTP](connector-sftp.md). Он поддерживается в качестве источника, но не для приемника.

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Excel.

| Свойство.         | Описание                                                  | Обязательно |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство Type набора данных должно иметь значение **Excel**.   | Да      |
| location         | Параметры расположения файлов. Каждый файловый соединитель имеет собственный тип расположения и поддерживаемые свойства в разделе `location` . | Да      |
| sheetName        | Имя листа Excel для чтения данных.                       | Да      |
| range            | Диапазон ячеек на заданном листе для нахождение выборочных данных, например `A3:H5` (таблица из a3 в H5), `A3` (таблица, начинающаяся из ячейки a3), `A3:A3` (одна ячейка). Если не указано, ADF считывает данные из листа целиком в виде таблицы. | Нет       |
| firstRowAsHeader | Указывает, следует ли рассматривать первую строку в заданном листе или диапазоне как строку заголовка с именами столбцов.<br>Допустимые значения: **true** и **false** (по умолчанию). | Нет       |
| nullValue        | Задает строковое представление значения NULL. <br>Значение по умолчанию — **пустая строка**. | Нет       |
| компрессионкодек | Кодек сжатия, используемый для чтения файлов Excel. <br>Допустимые значения: **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **привязки**или **lz4**. Значение по умолчанию не сжато. <br>**Примечание** . в настоящее время действие копирования не поддерживает "привязку" & "lz4", а поток данных сопоставления не поддерживает "ZipDeflate". <br>**Примечание** . при использовании действия копирования для распаковки файлов **ZipDeflate** и записи в хранилище файловых данных приемника файлы извлекаются в папку: `<path specified in dataset>/<folder named as source zip file>/` . | Нет       |
| compressionLevel | Коэффициент сжатия. <br>Допустимые значения: **оптимальный** или **самый быстрый**.<br>- **Самый быстрый:** Операция сжатия должна завершиться как можно быстрее, даже если итоговый файл не сжимается оптимально.<br>- **Оптимально**. операция сжатия должна быть оптимально сжата, даже если выполнение операции займет больше времени. Дополнительные сведения см. в разделе [Уровень сжатия](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx). | Нет       |

Ниже приведен пример набора данных Excel в хранилище BLOB-объектов Azure.

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником Excel.

### <a name="excel-as-source"></a>Excel в качестве источника 

В разделе *** \* Источник \* *** действия копирования поддерживаются следующие свойства.

| Свойство.      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство Type источника действия копирования должно иметь значение **ексцелсаурце**. | Да      |
| сторесеттингс | Группа свойств для чтения данных из хранилища данных. Каждый файловый соединитель имеет собственные Поддерживаемые параметры чтения в разделе `storeSettings` . | Нет       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>Дальнейшие шаги

- [Действие копирования в фабрике данных Azure](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
