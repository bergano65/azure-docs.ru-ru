---
title: Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в Azure Data Lake Store или из Data Lake Store в поддерживаемые приемники хранилищ с помощью фабрики данных.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: jingwang
ms.openlocfilehash: 5ff6b255e5509383bc0999a93e056810a9eefb84
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080622"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Копирование данных в Azure Data Lake Storage Gen1 и из него с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1](v1/data-factory-azure-datalake-connector.md)
> * [Текущая версия](connector-azure-data-lake-store.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Azure Data Lake Storage Gen1 (ранее Azure Data Lake Store) и в него. Она составлена на основе статьи [Действие копирования в фабрике данных Azure](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из любого поддерживаемого в качестве источника хранилища данных в Azure Data Lake Store или из Azure Data Lake Store в любое поддерживаемое в качестве приемника хранилище данных. См. таблицу [поддерживаемых хранилищ данных](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Azure Data Lake Store поддерживает:

- Копирование файлов с использованием одного из следующих методов аутентификации: на основе **субъекта-службы** или **управляемого удостоверения ресурсов Azure**.
- Копирование файлов "как есть", анализ или создание файлов с использованием [поддерживаемых форматов файлов и кодеков сжатия](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Если вы копируете данные с использованием локальной среды выполнения интеграции, настройте корпоративный брандмауэр, чтобы разрешить исходящий трафик в `<ADLS account name>.azuredatalakestore.net` и `login.microsoftonline.com/<tenant>/oauth2/token` через порт 443. Последняя – это служба токенов безопасности Azure, с которой должна взаимодействовать среда выполнения интеграции для получения маркера доступа.

## <a name="get-started"></a>Начало работы

> [!TIP]
> Пошаговое руководство по использованию соединителя Azure Data Lake Store см. в разделе [Загрузка данных в Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для Azure Data Lake Store.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Data Lake Store поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства `type` следует задать значение **AzureDataLakeStore**. | Yes |
| dataLakeStoreUri | Сведения об учетной записи Azure Data Lake Store. Эти данные принимают один из следующих форматов: `https://[accountname].azuredatalakestore.net/webhdfs/v1` или `adl://[accountname].azuredatalakestore.net/`. | Yes |
| subscriptionId | Идентификатор подписки Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| имя_группы_ресурсов | Имя группы ресурсов Azure, к которой принадлежит учетная запись Data Lake Store. | Необходимо для приемника |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции (если хранилище данных расположено в частной сети). Если это свойство не указано, оно использует интегрированную среду выполнения Azure по умолчанию. |Нет  |

### <a name="use-service-principal-authentication"></a>Использование аутентификации субъекта-службы

При использовании проверки подлинности на основе субъекта-службы необходимо зарегистрировать сущность приложения в Azure Active Directory и предоставить ей доступ к Data Lake Store. Подробные инструкции см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Запишите следующие значения, которые используются для определения связанной службы:

- Идентификатор приложения
- Ключ приложения
- Tenant ID

>[!IMPORTANT]
> Убедитесь, что в Data Lake Store субъекту-службе предоставлено правильное разрешение.
>- **В качестве источника**. Выбрав **Обозреватель данных** > **Доступ**, назначьте как минимум разрешение **Чтение и выполнение** для просмотра и копирования файлов в папках и подпапках. Можно также предоставить разрешение **Чтения** для копирования одного файла. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Требования к управлению доступом на уровне учетной записи (IAM) не отсутствуют.
>- **В качестве приемника**. Выбрав **Обозреватель данных** > **Доступ**, предоставьте по крайней мере разрешение **Запись и выполнение** для создания дочерних элементов в папке. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Если для копирования используется среда выполнения интеграции Azure (источник и приемник находятся в облаке), чтобы в решении "Фабрика данных" можно было определить регион Data Lake Store, назначьте в IAM роль не ниже уровня **читателя**. Если вы хотите избежать этой роли IAM, явным образом [создайте среду выполнения интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) с расположением Data Lake Store. Как показано в следующем примере, ассоциируйте ее в связанной службе Data Lake Store.

>[!NOTE]
>Для отображения папок, начиная с корня, необходимо задать разрешения для субъекта-службы, которое предоставляется **на корневом уровне с разрешением "Выполнение"**. Это работает, если вы используете:
>- **средство копирования данных** для разработки конвейера копирования;
>- **пользовательский интерфейс фабрики данных** для проверки подключения и перемещения по папкам во время разработки.

Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Yes |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как `SecureString`, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes |

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

Фабрика данных может быть связана с [управляемым удостоверением для ресурсов Azure](data-factory-service-identity.md), которое представляет эту фабрику данных. Это удостоверение службы можно использовать для аутентификации Data Lake Store, так же как и собственный субъект-службу. Оно разрешает назначенной фабрике обращаться к данным и копировать их из службы Data Lake Store и в нее.

Использование аутентификации управляемых удостоверений для ресурсов Azure.

1. [Получите удостоверение службы фабрики данных](data-factory-service-identity.md#retrieve-service-identity), скопировав значение идентификатора приложения удостоверения службы Service Identity Application ID, созданного вместе с фабрикой.
2. Разрешите удостоверению службы обращаться к Data Lake Store так же, как и субъекту-службе, руководствуясь этими указаниями.

>[!IMPORTANT]
> Убедитесь, что в Data Lake Store удостоверению службы фабрики данных предоставлено правильное разрешение.
>- **В качестве источника**. Выбрав **Обозреватель данных** > **Доступ**, назначьте как минимум разрешение **Чтение и выполнение** для просмотра и копирования файлов в папках и подпапках. Можно также предоставить разрешение **Чтения** для копирования одного файла. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Требования к управлению доступом на уровне учетной записи (IAM) не отсутствуют.
>- **В качестве приемника**. Выбрав **Обозреватель данных** > **Доступ**, предоставьте по крайней мере разрешение **Запись и выполнение** для создания дочерних элементов в папке. Вы можете выбрать добавление в **эту папку и все дочерние элементы** для свойства recursive и добавить в качестве **записи разрешения доступа и записи разрешения по умолчанию**. Если для копирования используется среда выполнения интеграции Azure (источник и приемник находятся в облаке), чтобы в решении "Фабрика данных" можно было определить регион Data Lake Store, назначьте в IAM роль не ниже уровня **читателя**. Если вы хотите избежать этой роли IAM, явным образом [создайте среду выполнения интеграции Azure](create-azure-integration-runtime.md#create-azure-ir) с расположением Data Lake Store. Как показано в следующем примере, ассоциируйте ее в связанной службе Data Lake Store.

>[!NOTE]
>Для отображения папок, начиная с корня, необходимо задать разрешения для субъекта-службы, которое предоставляется **на корневом уровне с разрешением "Выполнение"**. Это работает, если вы используете:
>- **средство копирования данных** для разработки конвейера копирования;
>- **пользовательский интерфейс фабрики данных** для проверки подключения и перемещения по папкам во время разработки.

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

Чтобы скопировать данные в Azure Data Lake Store или из него, укажите для свойства `type`набора данных значение **AzureDataLakeStoreFile**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type набора данных необходимо задать следующее значение: **AzureDataLakeStoreFile** |Yes |
| folderPath | Путь к папке в Data Lake Store. Если это свойство не указано, будет использоваться корневая папка. <br/><br/>Фильтр с подстановочными знаками поддерживается. Допустимые подстановочные знаки: `*` (соответствует нулю или большему количеству знаков) и `?` (соответствует нулю или одному знаку). Для экранирования используйте `^`, если имя фактического файла содержит подстановочный знак или escape-символ. <br/><br/>Примеры: rootfolder/subfolder/. Дополнительные примеры см. в разделе [Примеры фильтров папок и файлов](#folder-and-file-filter-examples). |Нет  |
| fileName | **Имя или фильтр шаблонов** для файлов по указанному folderPath. Если этому свойству не присвоить значение, набор данных будет указывать на все файлы в папке. <br/><br/>Допустимые знаки подстановки для фильтра: `*` (соответствует нулю или нескольким символам) и `?` (соответствует нулю или одному символу).<br/>Пример 1. `"fileName": "*.csv"`<br/>Пример 2. `"fileName": "???20180427.txt"`<br/>Используйте `^` для экранирования символов, если фактическое имя файла содержит подстановочный знак или этот escape-символ.<br/><br/>Если fileName для выходного набора данных не указан, а **preserveHierarchy** не указан в приемнике действия, действие копирования автоматически создаст имя файла по следующему шаблону: "*Data.[GUID идентификатора выполнения действия].[GUID, если поведение – FlattenHierarchy].[формат, если настроено].[сжатие, если настроено]*". Например, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". При копировании из табличного источника с использованием имени таблицы вместо запроса формат имени будет таким: "*[имя таблицы].[фoрмат].[сжатие, если настроено]*". Например, "MyTable.csv". |Нет  |
| свойства | Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных.<br/><br/>Если необходимо проанализировать или создать файлы определенного формата, поддерживаются следующие форматы файлов: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](supported-file-formats-and-compression-codecs.md#text-format), [формате Json](supported-file-formats-and-compression-codecs.md#json-format), [формате Avro](supported-file-formats-and-compression-codecs.md#avro-format), [формате Orc](supported-file-formats-and-compression-codecs.md#orc-format) и [ формате Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Нет (только для сценария двоичного копирования) |
| compression | Укажите тип и уровень сжатия данных. Дополнительные сведения см. в разделе [Поддержка сжатия](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**.<br/>Поддерживаемые уровни: **Оптимальный** и **Самый быстрый**. |Нет  |


>[!TIP]
>Чтобы скопировать все файлы в папке, укажите только **folderPath**.<br>Чтобы скопировать один файл с определенным именем, укажите **folderPath** с частью папки и **fileName** с именем файла.<br>Чтобы скопировать подмножество файлов в папке, укажите **folderPath** с частью папки и **fileName** с фильтром подстановочных знаков. 

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
            "fileName": "myfile.csv.gz",
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

Чтобы скопировать данные из Data Lake Store, задайте тип источника **AzureDataLakeStoreSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство `type` источника действия копирования должно иметь значение **AzureDataLakeStoreSource** |Yes |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. Обратите внимание, что если для свойства `recursive` задано значение true, а приемником является файловое хранилище, пустые папки и вложенные папки не создаются в приемнике. Допустимые значения: **true** (по умолчанию) и **false**. | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

Чтобы копировать данные в Data Lake Store, установите тип приемника **AzureDataLakeStoreSink** в действии копирования. Следующие свойства поддерживаются в разделе **sink**:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство `type` приемника действия копирования должно иметь следующее значение: **AzureDataLakeStoreSink** |Yes |
| copyBehavior | Определяет поведение копирования, когда источником являются файлы из файлового хранилища данных.<br/><br/>Допустимые значения:<br/><b>- PreserveHierarchy (по умолчанию)</b>. Сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><b>– FlattenHierarchy</b>. Все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированное имя. <br/><b>- MergeFiles</b>. Объединяет все файлы из исходной папки в один файл. Если указано имя Blob-объекта или имя файла, то оно присваивается объединенному файлу. В противном случае имя файла создается автоматически. | Нет  |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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
| `Folder*` | (пусто, используйте по умолчанию) | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | (пусто, используйте по умолчанию) | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | false | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5.csv<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |
| `Folder*` | `*.csv` | Да | ПапкаA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Файл1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Файл5.csv**<br/>Другая_папкаB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Примеры поведения операции копирования

В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений `recursive` и `copyBehavior`.

| recursive | copyBehavior | Структура папок источника | Результаты цели |
|:--- |:--- |:--- |:--- |
| Да |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая "Папка1" создается с такой же структурой, как и исходная папка:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| false |preserveHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |flattenHierarchy | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| false |mergeFiles | Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 | Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
