---
title: Отказоустойчивость действия копирования в фабрике данных Azure | Документация Майкрософт
description: Сведения о добавлении отказоустойчивости для действия копирования в фабрике данных Azure с помощью пропуска несовместимых строк.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: jingwang
ms.openlocfilehash: 3f207cdb3af3f7e328cd5843053240bbbe15980e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418349"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Отказоустойчивость действия копирования в фабрике данных Azure
> [!div class="op_single_selector" title1="Выберите версию услуги Data Factory, которую вы используете:"]
> * [Версия 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Текущая версия](copy-activity-fault-tolerance.md)

Для действия копирования фабрики данных Azure предусмотрены разные способы устранения несовместимых строк при копировании данных между хранилищами данных источника и приемника.

- Вы можете прервать и отменить действие копирования при обнаружении несовместимых данных (действие по умолчанию).
- Вы можете продолжать копировать все данные, обеспечив отказоустойчивость с помощью пропуска несовместимых строк данных. Кроме того, вы можете записать несовместимые строки в журнал и сохранить его в хранилище BLOB-объектов Azure или Azure Data Lake Store. Этот журнал затем можно просмотреть, чтобы узнать причину сбоя, исправить данные в источнике данных и повторить действие копирования.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.
Действие копирования поддерживает три сценария для обработки несовместимых данных: обнаружение, пропуск и запись в журнал.

- **Несовместимость между типом данных источника и собственным типом приемника**. 

    Пример. Выполняется копирование данных из CSV-файла в хранилище BLOB-объектов в базу данных SQL с использованием определения схемы, которое содержит три столбца типа INT. Строки CSV-файла, которые содержат числовые данные, например 123,456,789, успешно копируются в хранилище приемника. А строки, которые содержат нечисловые значения, например 123,456, abc, определяются как несовместимые и пропускаются.

- **Несоответствие числа столбцов между источником и приемником**.

    Пример. Выполняется копирование данных из CSV-файла в хранилище BLOB-объектов в базу данных SQL с использованием определения схемы, которое содержит шесть столбцов. Строки CSV-файла, которые содержат шесть столбцов, успешно копируются в хранилище приемника. Строки CSV-файла, которые содержат больше или меньше шести столбцов, определяются как несовместимые и пропускаются.

- **Нарушение первичного ключа при записи в SQL Server, базу данных SQL Azure или Azure Cosmos DB**.

    Пример. Выполняется копирование данных из SQL Server в базу данных SQL. Определен первичный ключ в базе данных SQL приемников, но такой первичный ключ не определен на исходном сервере SQL Server. Повторяющиеся строки, которые существуют в источнике, не удается скопировать в приемник. Действие копирования копирует только первую строку из источника данных в приемник. Последующие строки в источнике, которые содержат повторяющиеся значения первичного ключа, определяются как несовместимые и пропускаются.

>[!NOTE]
>- Чтобы загрузить данные в хранилище данных SQL с помощью PolyBase, настройте встроенную в PolyBase поддержку отказоустойчивости, указав политики отклонения с помощью параметра [polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink) в действии копирования. По-прежнему можно включить перенаправление несовместимых строк PolyBase в BLOB-объект или ADLS в обычном режиме, как показано ниже.
>- Эта функция не применяется, когда действие копирования настроено на вызов [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


## <a name="configuration"></a>Параметр Configuration
В следующем примере приводится определение JSON для настройки пропуска несовместимых строк в действии копирования:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Указывает, следует ли пропустить несовместимые строки во время копирования или нет. | Истина<br/>False (по умолчанию) | Нет 
redirectIncompatibleRowSettings | Группа свойств, которые можно указать, если вы хотите записать несовместимые строки в журнал. | &nbsp; | Нет 
linkedServiceName | Связанная [служба хранилища Azure](connector-azure-blob-storage.md#linked-service-properties) или [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties), в которой будет храниться журнал, содержащий пропущенные строки. | Имя связанной службы `AzureStorage` или `AzureDataLakeStore`, которая ссылается на экземпляр хранилища, используемый для хранения файла журнала. | Нет 
path | Путь к файлу журнала, который содержит пропущенные строки. | Укажите путь к журналу, в котором будут храниться несовместимые данные. Если путь не указан, служба создаст контейнер самостоятельно. | Нет 

## <a name="monitor-skipped-rows"></a>Мониторинг пропущенных строк
Когда действие копирования будет завершено, вы увидите число пропущенных строк в выходных данных действия копирования:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
При настройке записи несовместимых строк файл журнала можно найти по этому пути: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Файлы журнала должны быть представлены только в формате CSV. Пропущенные исходные данные при необходимости регистрируются с использованием запятой в качестве разделителя столбцов. Добавим к исходным данным источника в файле журнала два дополнительных столбца — ErrorCode и ErrorMessage. В них содержатся сведения о первопричине несовместимости. ErrorCode и ErrorMessage указываются в двойных кавычках. 

Ниже приведен пример содержимого файла журнала:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Дополнительная информация
См. другие статьи о действиях копирования:

- [Действие копирования в фабрике данных Azure](copy-activity-overview.md)
- [Руководство по настройке производительности действия копирования](copy-activity-performance.md)


