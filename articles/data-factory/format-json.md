---
title: Формат JSON на фабрике данных Azure
description: В этой теме описывается, как бороться с форматом JSON на фабрике данных Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260648"
---
# <a name="json-format-in-azure-data-factory"></a>Формат JSON на фабрике данных Azure

Следуйте этой статье, когда вы хотите **разобрать файлы JSON или написать данные в формате JSON**. 

Формат JSON поддерживается для следующих разъемов: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)и [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе приводится список свойств, поддерживаемых набором данных JSON.

| Свойство         | Описание                                                  | Обязательно |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Свойство типа набора данных должно быть установлено **на Json.** | Да      |
| location         | Настройки расположения файла (ы). Каждый файл на основе разъема имеет `location`свой собственный тип местоположения и поддерживается свойства под . **Подробности смотрите в разделе разъем -> свойства dataset**. | Да      |
| encodingName     | Тип кодирования, используемый для чтения/записи тестовых файлов. <br>Разрешенные значения: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM27", "IBM", "IBM", "IBM", "IBM", "IBM", "IBM IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", IBM , "ИСО-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ИСО-8859-6", "ИСО-8859-7", "ИСО-8859-8", "ИСО-8859-9", "ИСО-8859-13", "ИСО-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-121",, "WINDOWS-121"," ОКНА-1252", "ОКНА-1253", "ОКНА-1254", "ОКНА-1255", "ОКНА-1256", "ОКНА-1257", "ОКНА-1258".| нет       |
| компрессионныйкод | Кодек сжатия, используемый для чтения/записи текстовых файлов. <br>Разрешенные значения **bzip2**, **gzip**, **сдувать**, **зиффлировать**, **быстро**, или **lz4**. использовать при сохранении файла. <br>Примечание в настоящее время Копирование деятельности не поддерживает "быстрый" & "lz4".<br>Обратите внимание при использовании копирования деятельности для декомпрессии файла (ы) и записи в `<path specified in dataset>/<folder named as source zip file>/`файл на основе хранения данных о раковине, файлы будут извлечены в папку: . | нет       |
| Уровень сжатия | Коэффициент сжатия. <br>Разрешенные значения **оптимальны** или **быстры.**<br>- **Самый быстрый:** Операция сжатия должна завершиться как можно быстрее, даже если полученный файл не сжимается оптимально.<br>- **Оптимальная**: Операция сжатия должна быть оптимально сжата, даже если операция занимает больше времени. Дополнительные сведения см. в разделе [Уровень сжатия](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx). | нет       |

Ниже приведен пример набора данных JSON по хранению Azure Blob:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником и раковиной JSON.

### <a name="json-as-source"></a>JSON как источник

Следующие свойства поддерживаются в разделе *** \*источника активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено **на JSONSource.** | Да      |
| storeSettings | Группа свойств о том, как считывать данные из хранилища данных. Каждый файл на основе разъема `storeSettings`имеет свои собственные поддерживаемые настройки чтения под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

### <a name="json-as-sink"></a>JSON как раковина

Следующие свойства поддерживаются в разделе *** \*раковины активности\* *** копирования.

| Свойство      | Описание                                                  | Обязательно |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Свойство типа источника активности копирования должно быть установлено на **JSONSink.** | Да      |
| форматНастройкы | Группа свойств. Обратитесь к таблице **настроек jSON** ниже. | нет       |
| storeSettings | Группа свойств о том, как записывать данные в хранилище данных. Каждый разъем на основе файлов имеет `storeSettings`свои собственные поддерживаемые настройки записи под . **Подробности смотрите в разделе разъем -> Свойства активности копирования**. | нет       |

Поддерживаемые **JSON написать настройки** под `formatSettings`:

| Свойство      | Описание                                                  | Обязательно                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Тип форматаНастройкы должны быть установлены на **JsonWriteSettings.** | Да                                                   |
| filePattern |Шаблон данных, хранящихся в каждом JSON-файле. Допустимые значения: **setOfObjects** и **arrayOfObjects**. Значение **по умолчанию** **— это setOfObjects.** Подробные сведения об этих шаблонах см. в разделе [Шаблоны файлов JSON](#json-file-patterns). |нет |

### <a name="json-file-patterns"></a>Шаблоны файлов JSON

Копирование активности может автоматически обнаруживать и разбирать следующие шаблоны файлов JSON. 

- **Тип 1: setOfObjects**

    Каждый файл содержит один объект или несколько разделенных строками или объединенных объектов. 
    При выборе этого параметра в поглотителе активности копирования активность копирования производит один файл JSON с каждым объектом на строку (линейно-разграниченный).

    * **Пример единого объекта JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Пример JSON-файла с разделителем-строкой**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Пример объединенного JSON-файла**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Тип 2: arrayOfObjects**

    Каждый файл содержит массив объектов.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

Типы файлов JSON могут использоваться как в качестве раковины, так и в качестве источника для картирования потока данных.

### <a name="creating-json-structures-in-a-derived-column"></a>Создание структур JSON в производной колонке

Вы можете добавить сложный столбец в поток данных через вылитого в себя систему выражения столбца. В производном преобразовании столбца добавьте новую колонку и откройте строитель выражения, нажав на синюю коробку. Чтобы сделать столбец сложным, можно ввести структуру JSON вручную или использовать UX для добавления подколок в интерактивном режиме.

#### <a name="using-the-expression-builder-ux"></a>Использование строителя выражения UX

В панели схемы вывода нависнетнадите над столбцом и щелкните значок plus. Выберите **подколонку Добавить,** чтобы сделать столбец сложным типом.

![Добавление подколонки](media/data-flow/addsubcolumn.png "Добавление подколонки")

Таким же образом можно добавить дополнительные столбцы и подколонны. Для каждого несложного поля в правом редакторе выражения может быть добавлено выражение.

![Сложная колонка](media/data-flow/complexcolumn.png "Сложная колонка")

#### <a name="entering-the-json-structure-manually"></a>Ввод структуры JSON вручную

Чтобы вручную добавить структуру JSON, добавьте новый столбец и введите выражение в редакторе. Выражение следует следующему общему формату:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Если бы это выражение было введено для столбца под названием "комплексная колонна", то оно было бы написано в раковину следующим JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Пример ручного сценария для полного иерархического определения
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Параметры исходного формата

Использование набора данных JSON в качестве источника в потоке данных позволяет установить пять дополнительных настроек. Эти настройки можно найти в **настройках JSON** на вкладке **Source Options.**  

![Настройки JSON](media/data-flow/json-settings.png "Параметры JSON")

#### <a name="default"></a>Значение по умолчанию

По умолчанию данные JSON считываемывся в следующем формате.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Единый документ

При выборе **единого документа** отображение потоков данных считывается по одному документу JSON из каждого файла. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Не цитируемые имена столбцов

При выборе **некотируемых имен столбцов** отображаются, потоки данных отображения считывает столбцы JSON, которые не окружены котировками. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Имеет комментарии

Выберите **имеет комментарии,** если данные JSON имеют C или C ' стиль комментариев.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Одиночный цитируемый

Выберите **одиночные котировки,** если поля и значения JSON используют одиночные котировки вместо двойных котировок.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Backslash бежал

Выберите **одиночные цитаты,** если backslashes используются для избежания символов в данных JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о действии копирования](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
