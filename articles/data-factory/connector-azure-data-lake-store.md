---
title: Копирование данных в Azure Data Lake Storage 1-го поколения или из него
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в Azure Data Lake Store или из Data Lake Store в поддерживаемые приемники хранилищ с помощью фабрики данных.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 17f7c62600bcc2aa21fbcea8ecd96810be412a26
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930534"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Копирование данных в Azure Data Lake Storage 1-го поколения с помощью фабрики данных Azure или из нее

> [!div class="op_single_selector" title1="Выберите версию фабрики данных Azure, которую вы используете:"]
> * [Версия 1](v1/data-factory-azure-datalake-connector.md)
> * [Текущая версия](connector-azure-data-lake-store.md)

В этой статье описано, как копировать данные в Azure Data Lake Storage 1-го поколения и обратно. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Azure Data Lake Storage 1-го поколения поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md) 
- [Поток данных сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Удалить действие](delete-activity.md)

В частности, с помощью этого соединителя можно:

- Скопируйте файлы с помощью одного из следующих методов проверки подлинности: субъект-служба или управляемые удостоверения для ресурсов Azure.
- Скопируйте файлы как есть или Проанализируйте или создайте файлы с [поддерживаемыми форматами файлов и кодеками сжатия](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> При копировании данных с помощью локальной среды выполнения интеграции Настройте корпоративный брандмауэр, чтобы разрешить исходящий трафик для `<ADLS account name>.azuredatalakestore.net` и `login.microsoftonline.com/<tenant>/oauth2/token` через порт 443. Последняя – это служба токенов безопасности Azure, с которой должна взаимодействовать среда выполнения интеграции для получения маркера доступа.

## <a name="get-started"></a>Начать

> [!TIP]
> Пошаговые инструкции по использованию соединителя Azure Data Lake Store см. в разделе [Загрузка данных в Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к Azure Data Lake Store.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Data Lake Store поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Для свойства `type` следует задать значение **AzureDataLakeStore**. | ДА |
| dataLakeStoreUri | Сведения об учетной записи Azure Data Lake Store. Эти данные принимают один из следующих форматов: `https://[accountname].azuredatalakestore.net/webhdfs/v1` или `adl://[accountname].azuredatalakestore.net/`. | ДА |
| subscriptionId | Идентификатор подписки Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| имя_группы_ресурсов | Имя группы ресурсов Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции, если хранилище данных находится в частной сети. Если это свойство не указано, используется среда выполнения интеграции Azure по умолчанию. |Нет |

### <a name="use-service-principal-authentication"></a>Использование аутентификации субъекта-службы

Чтобы использовать проверку подлинности субъекта-службы, выполните следующие действия.

1. Зарегистрируйте сущность приложения в Azure Active Directory и предоставьте ей доступ к Data Lake Store. Подробные инструкции см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Запишите следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Предоставьте субъекту-службе соответствующее разрешение. См. примеры того, как разрешение работает в Data Lake Storage 1-го поколения из [контроля доступа в Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **В качестве источника**: в **обозревателе данных** > **доступ**предоставьте по крайней мере разрешение на **выполнение** для всех исходящих папок, включая корневой каталог, а также разрешение на **Чтение** файлов для копирования. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Нет необходимости в контроле доступа на уровне учетной записи (IAM).
    - В **качестве приемника**: в **обозревателе данных** > **доступ**предоставьте по крайней мере разрешение на **выполнение** для всех исходящих папок, включая корневой каталог, а также разрешение на **запись** для папки приемника. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Если вы используете среду выполнения интеграции Azure для копирования (источник и приемник находятся в облаке), в IAM предоставьте по крайней мере роль **читателя** , чтобы фабрика данных определяла регион для Data Lake Store. Если вы хотите избежать этой роли IAM, явным образом [создайте среду выполнения интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) с расположением Data Lake Store. Например, если Data Lake Store находится в Западной Европе, создайте среду выполнения интеграции Azure с параметром location, имеющим значение "Западная Европа". Свяжите их со связанной службой Data Lake Store, как показано в следующем примере.

Поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| servicePrincipalId | Укажите идентификатора клиента приложения. | ДА |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как `SecureString`, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | ДА |
| tenant | Укажите сведения о клиенте, такие как доменное имя или идентификатор клиента, в котором находится ваше приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | ДА |

**Пример.**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Использование аутентификации управляемых удостоверений для ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Вы можете напрямую использовать это управляемое удостоверение для проверки подлинности Data Lake Store, как и при использовании собственного субъекта-службы. Оно разрешает назначенной фабрике обращаться к данным и копировать их из службы Data Lake Store и в нее.

Чтобы использовать управляемые удостоверения для проверки подлинности ресурсов Azure, выполните следующие действия.

1. [Получите сведения об управляемом удостоверении фабрики данных](data-factory-service-identity.md#retrieve-managed-identity) , СКОПИРОВАВ значение идентификатора приложения удостоверения службы, созданного вместе с фабрикой.

2. Предоставьте управляемому удостоверению доступ к Data Lake Store. См. примеры того, как разрешение работает в Data Lake Storage 1-го поколения из [контроля доступа в Azure Data Lake Storage 1-го поколения](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **В качестве источника**: в **обозревателе данных** > **доступ**предоставьте по крайней мере разрешение на **выполнение** для всех исходящих папок, включая корневой каталог, а также разрешение на **Чтение** файлов для копирования. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Нет необходимости в контроле доступа на уровне учетной записи (IAM).
    - В **качестве приемника**: в **обозревателе данных** > **доступ**предоставьте по крайней мере разрешение на **выполнение** для всех исходящих папок, включая корневой каталог, а также разрешение на **запись** для папки приемника. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Если вы используете среду выполнения интеграции Azure для копирования (источник и приемник находятся в облаке), в IAM предоставьте по крайней мере роль **читателя** , чтобы фабрика данных определяла регион для Data Lake Store. Если вы хотите избежать этой роли IAM, явным образом [создайте среду выполнения интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) с расположением Data Lake Store. Свяжите их со связанной службой Data Lake Store, как показано в следующем примере.

В фабрике данных Azure не нужно указывать ничего, кроме общих сведений о Data Lake Store в связанной службе.

**Пример.**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Для Azure Data Lake Store Gen1 в параметрах `location` в наборе данных на основе формата поддерживаются следующие свойства:

| Свойство   | Описание                                                  | Обязательно для заполнения |
| ---------- | ------------------------------------------------------------ | -------- |
| Тип       | Свойство Type в разделе `location` в наборе данных должно иметь значение **азуредаталакесторелокатион**. | ДА      |
| folderPath | Путь к папке. Если вы хотите использовать подстановочный знак для фильтрации папок, пропустите этот параметр и укажите его в параметрах источника действия. | Нет       |
| fileName   | Имя файла в заданной folderPath. Если вы хотите использовать подстановочный знак для фильтрации файлов, пропустите этот параметр и укажите его в параметрах источника действия. | Нет       |

**Пример.**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>Устаревшая модель набора данных

>[!NOTE]
>Следующая модель набора данных по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать новую модель, упомянутую выше в разделе "вперед", и пользовательский интерфейс создания ADF переключился на создание новой модели.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство Type набора данных должно иметь значение **AzureDataLakeStoreFile**. |ДА |
| folderPath | Путь к папке в Data Lake Store. Если это свойство не указано, будет использоваться корневая папка. <br/><br/>Поддерживается фильтр с подстановочными знаками. Разрешенные подстановочные знаки `*` (соответствует нулю или большему числу символов) и `?` (соответствует нулю или одиночному символу). Используйте `^` для экранирования, если фактическое имя папки содержит подстановочный знак или escape-символ внутри. <br/><br/>Например: RootFolder/вложенная папка/. Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Нет |
| fileName | Имя или фильтр по шаблону для файлов с указанной "folderPath". Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Для фильтра разрешены подстановочные знаки `*` (соответствует нулю или большему числу символов) и `?` (соответствует нулю или одиночному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования, если фактическое имя файла имеет подстановочный знак или escape-символ внутри.<br/><br/>Если параметр fileName не указан для выходного набора данных, а **preserveHierarchy** не указан в приемнике действия, действие копирования автоматически создает имя файла со следующим форматом: "*Data. [ Идентификатор запуска действия GUID]. [GUID if FlattenHierarchy]. [Format, если настроено]. [compression, если он настроен]* ", например" Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz ". При копировании из табличного источника с использованием имени таблицы вместо запроса используется шаблон имени " *[имя таблицы]. [ format]. [compression, если он настроен]* ", например" MyTable. csv ". |Нет |
| modifiedDatetimeStart | Фильтр файлов, основанный на последнем измененном атрибуте. Файлы выбираются, если время последнего изменения находится в пределах диапазона времени между `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время применяется к часовому поясу UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Включение этого параметра влияет на общую производительность перемещения данных, если требуется использовать фильтр файлов с огромным объемом файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не применяется к набору данных. Если `modifiedDatetimeStart` имеет значение DateTime, но `modifiedDatetimeEnd` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых больше или равен значению DateTime. Если `modifiedDatetimeEnd` имеет значение DateTime, но `modifiedDatetimeStart` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых меньше значения DateTime.| Нет |
| modifiedDatetimeEnd | Фильтр файлов, основанный на последнем измененном атрибуте. Файлы выбираются, если время последнего изменения находится в пределах диапазона времени между `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время применяется к часовому поясу UTC в формате "2018-12-01T05:00:00Z". <br/><br/> Включение этого параметра влияет на общую производительность перемещения данных, если требуется использовать фильтр файлов с огромным объемом файлов. <br/><br/> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не применяется к набору данных. Если `modifiedDatetimeStart` имеет значение DateTime, но `modifiedDatetimeEnd` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых больше или равен значению DateTime. Если `modifiedDatetimeEnd` имеет значение DateTime, но `modifiedDatetimeStart` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых меньше значения DateTime.| Нет |
| свойства | Если требуется копировать файлы между хранилищами на основе файлов (двоичное копирование), пропустите раздел Format в определениях входного и выходного наборов данных.<br/><br/>Если нужно проанализировать или создать файлы определенного формата, поддерживаются следующие типы форматов файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе **format** необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате JSON](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Optimal** и **Fastest**. |Нет |

>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать отдельный файл с определенным именем, укажите параметр **FolderPath** с именем файла **, который является** частью папки.<br>Чтобы скопировать подмножество файлов в папке, укажите параметр **FolderPath** с частью папки и **именем файла** с фильтром с подстановочными знаками. 

**Пример.**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store в качестве источника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Следующие свойства поддерживаются для Azure Data Lake Store Gen1 в разделе Параметры `storeSettings` в источнике копирования на основе формата:

| Свойство                 | Описание                                                  | Обязательно для заполнения                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Тип                     | Свойство Type в разделе `storeSettings` должно иметь значение **азуредаталакесторереадсеттинг**. | ДА                                           |
| recursive                | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Если параметру recursive присвоено значение true и приемник является хранилищем на основе файлов, пустая папка или подпапка не копируется или не создается в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет                                            |
| вилдкардфолдерпас       | Путь к папке с подстановочными знаками для фильтрации исходных папок. <br>Разрешенные подстановочные знаки `*` (соответствует нулю или большему числу символов) и `?` (соответствует нулю или одиночному символу). Используйте `^` для экранирования, если фактическое имя папки содержит подстановочный знак или escape-символ внутри. <br>Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Нет                                            |
| вилдкардфиленаме         | Имя файла с подстановочными знаками в заданной folderPath/Вилдкардфолдерпас для фильтрации исходных файлов. <br>Разрешенные подстановочные знаки `*` (соответствует нулю или большему числу символов) и `?` (соответствует нулю или одиночному символу). Используйте `^` для экранирования, если фактическое имя папки содержит подстановочный знак или escape-символ внутри. Дополнительные примеры приведены в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). | Да, если `fileName` не указан в наборе данных |
| modifiedDatetimeStart    | Фильтр файлов, основанный на последнем измененном атрибуте. Файлы выбираются, если время последнего изменения находится в пределах диапазона времени между `modifiedDatetimeStart` и `modifiedDatetimeEnd`. Время применяется к часовому поясу UTC в формате "2018-12-01T05:00:00Z". <br> Свойства могут иметь значение NULL, что означает, что фильтр атрибутов файла не применяется к набору данных. Если `modifiedDatetimeStart` имеет значение DateTime, но `modifiedDatetimeEnd` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых больше или равен значению DateTime. Если `modifiedDatetimeEnd` имеет значение DateTime, но `modifiedDatetimeStart` имеет значение NULL, это означает, что выбраны файлы, атрибут последнего изменения которых меньше значения DateTime. | Нет                                            |
| modifiedDatetimeEnd      | То же, что и выше.                                               | Нет                                            |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу хранилища. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет                                            |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="legacy-source-model"></a>Устаревшая исходная модель

>[!NOTE]
>Следующая исходная модель копирования по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать новую модель, упомянутую выше, и пользовательский интерфейс создания ADF переключился на создание новой модели.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство `type` источника действия копирования должно иметь значение **AzureDataLakeStoreSource**. |ДА |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Если для `recursive` задано значение true и приемник является хранилищем на основе файлов, пустая папка или подпапка не копируется и не создается в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу данных. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store в качестве приемника

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Для Azure Data Lake Store Gen1 в параметрах `storeSettings` в приемнике копирования на основе формата поддерживаются следующие свойства:

| Свойство                 | Описание                                                  | Обязательно для заполнения |
| ------------------------ | ------------------------------------------------------------ | -------- |
| Тип                     | Свойство Type в разделе `storeSettings` должно иметь значение **азуредаталакесторевритесеттинг**. | ДА      |
| copyBehavior             | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. | Нет       |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу данных. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет       |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Устаревшая модель приемника

>[!NOTE]
>Следующая модель приемника копирования по-прежнему поддерживается "как есть" для обеспечения обратной совместимости. Рекомендуется использовать новую модель, упомянутую выше, и пользовательский интерфейс создания ADF переключился на создание новой модели.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| Тип | Свойство `type` приемника действия копирования должно иметь значение **AzureDataLakeStoreSink**. |ДА |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>— PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>— FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированные имена. <br/><b>— MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя файла, то оно присваивается объединенному файлу. В противном случае имя файла создается автоматически. | Нет |
| maxConcurrentConnections | Число подключений для одновременного подключения к хранилищу данных. Укажите, только если требуется ограничить одновременный подключение к хранилищу данных. | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Примеры фильтров папок и файлов

В этом разделе описываются результаты применения фильтров с подстановочными знаками к пути папки и имени файла.

| folderPath | fileName | recursive | Структура исходной папки и результат фильтрации (извлекаются файлы, выделенные **полужирным** шрифтом)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Пусто, используйте значение по умолчанию) | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | (Пусто, используйте значение по умолчанию) | true | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | true | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Примеры поведения операции копирования

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений `recursive` и `copyBehavior`.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая "Папка1" создается с такой же структурой, как и исходная папка:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| true |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл5" |
| true |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + file2 + Файл3 + "Файл4" + "Файл5" содержимое объединяется в один файл с автоматически сформированным именем файла. |
| false |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Subfolder1 с Файл3, "Файл4" и "Файл5" не забирается. |
| false |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для "Файл2"<br/><br/>Subfolder1 с Файл3, "Файл4" и "Файл5" не забирается. |
| false |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;файлы file1 + file2 объединяются в один файл с автоматически созданным именем файла. автоматически созданное имя для "Файл1"<br/><br/>Subfolder1 с Файл3, "Файл4" и "Файл5" не забирается. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Сохранение списков управления доступом в Data Lake Storage 2-го поколения

Если требуется реплицировать списки управления доступом (ACL) вместе с файлами данных при обновлении с Data Lake Storage 1-го поколения до Data Lake Storage 2-го поколения, см. раздел [сохранение списков ACL из Data Lake Storage 1-го поколения](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

Дополнительные сведения о преобразовании [источника](data-flow-source.md) и [преобразования приемников](data-flow-sink.md) в функции потока данных сопоставления.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="delete-activity-properties"></a>Свойства действия удаления

Чтобы получить сведения о свойствах, проверьте [действие Удалить](delete-activity.md) .

## <a name="next-steps"></a>Дальнейшие действия

В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
