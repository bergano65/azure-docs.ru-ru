---
title: Копирование данных из Teradata разных с помощью фабрики данных Azure | Документация Майкрософт
description: Соединитель Teradata службы фабрики данных позволяет копировать данные из Teradata разных в хранилища данных, поддерживаемые фабрикой данных в качестве приемников.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: b91ec46d47814418de21e9afe3e6e5534473c921
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008967"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Копирование данных из Teradata разных с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
>
> * [Версия 1](v1/data-factory-onprem-teradata-connector.md)
> * [Текущая версия](connector-teradata.md)

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные из Teradata разных. Она основана на [обзоре действия копирования](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Teradata поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой исходной матрицей](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)

Данные из Teradata разных можно скопировать в любое поддерживаемое хранилище данных, которое является приемником. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Teradata поддерживает:

- Teradata **версии 14,10, 15,0, 15,10, 16,0, 16,10 и 16,20**.
- Копирование данных с использованием **обычной** или проверки подлинности **Windows** .
- Параллельное копирование из источника Teradata. Дополнительные сведения см. в разделе [Параллельное копирование из Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> После выпуска локальной среды выполнения интеграции v 3.18, фабрика данных Azure обновила соединитель Teradata. Любая существующая Рабочая нагрузка, использующая предыдущий соединитель Teradata, по-прежнему поддерживается. Однако для новых рабочих нагрузок рекомендуется использовать новый. Обратите внимание, что для нового пути требуется другой набор связанных служб, наборов данных и источника копирования. Сведения о конфигурации см. в соответствующих разделах ниже.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Среда выполнения интеграции предоставляет встроенный драйвер Teradata, начиная с версии 3,18. Устанавливать драйверы вручную не требуется. Для драйвера требуется "визуальный C++ распространяемый компонент 2012 с обновлением 4" на компьютере с локальной средой выполнения интеграции. Если он еще не установлен, скачайте его [отсюда](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

Для любой локальной среды выполнения интеграции, более ранней, чем 3,18, установите [поставщик данных .NET для Teradata](https://go.microsoft.com/fwlink/?LinkId=278886)версии 14 или более поздней на компьютере со средой выполнения интеграции. 

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Teradata.

## <a name="linked-service-properties"></a>Свойства связанной службы

Связанная служба Teradata поддерживает следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **Teradata**. | Да |
| connectionString | Задает сведения, необходимые для подключения к экземпляру Teradata. Ознакомьтесь с приведенными ниже примерами.<br/>Можно также добавить пароль в Azure Key Vault и `password` извлечь конфигурацию из строки подключения. Дополнительные сведения см. в разделе [хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md) . | Да |
| username | Укажите имя пользователя для подключения к Teradata. Применяется при использовании проверки подлинности Windows. | Нет |
| password | Укажите пароль для учетной записи пользователя, указанной для имени пользователя. Можно также указать ссылку на [секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). <br>Применяется при использовании проверки подлинности Windows или при создании ссылки на пароль в Key Vault для обычной проверки подлинности. | Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Да |

**Пример использования обычной проверки подлинности**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример использования проверки подлинности Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Следующие полезные данные по-прежнему поддерживаются. Однако в дальнейшем следует использовать новый.

**Предыдущие полезные данные:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

В этом разделе содержится список свойств, поддерживаемых набором данных Teradata. Полный список разделов и свойств, доступных для определения наборов данных, см. в разделе [наборы данных](concepts-datasets-linked-services.md).

Для копирования данных из Teradata поддерживаются следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение `TeradataTable`. | Да |
| database | Имя экземпляра Teradata. | Нет (если свойство query указано в источнике действия) |
| table | Имя таблицы в экземпляре Teradata. | Нет (если свойство query указано в источнике действия) |

**Пример.**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`набор данных типа по-прежнему поддерживается. Однако рекомендуется использовать новый набор данных.

**Предыдущие полезные данные:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

В этом разделе содержится список свойств, поддерживаемых Teradata в качестве источника. Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata в качестве источника

>[!TIP]
>Чтобы эффективно загружать данные из Teradata с помощью секционирования данных, дополнительные сведения см. в разделе " [Параллельное копирование из Teradata](#parallel-copy-from-teradata) ".

Чтобы скопировать данные из Teradata, в разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type источника действия копирования должно иметь значение `TeradataSource`. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`.<br>При включении секционированной нагрузки необходимо привязать все соответствующие встроенные параметры секции в запросе. Примеры см. в разделе [Параллельное копирование из Teradata](#parallel-copy-from-teradata) . | Нет (если table в наборе данных указана) |
| partitionOptions | Задает параметры секционирования данных, используемые для загрузки данных из Teradata. <br>Допустимые значения: **Нет** (по умолчанию), **hash** и **динамикранже**.<br>Если параметр секции включен (то есть не `None`), степень параллелизма для параллельной загрузки данных из Teradata управляется [`parallelCopies`](copy-activity-performance.md#parallel-copy) параметром действия копирования. | Нет |
| partitionSettings | Укажите группу параметров для секционирования данных. <br>Применить, если параметр секционирования не `None`имеет значение. | Нет |
| partitionColumnName | Укажите имя исходного столбца, который будет использоваться секцией диапазона или хэш-секцией для параллельного копирования. Если этот параметр не указан, первичный индекс таблицы обнаруживается с автоопределением и используется в качестве столбца секционирования. <br>Применяется, если параметр Partition имеет `Hash` значение `DynamicRange`или. При использовании запроса для получения исходных данных, обработчика `?AdfHashPartitionCondition` или `?AdfRangePartitionColumnName` в предложении WHERE. См. пример в разделе [Параллельное копирование из Teradata](#parallel-copy-from-teradata) . | Нет |
| partitionUpperBound | Максимальное значение столбца секционирования для копирования данных. <br>Применить, если параметр секционирования имеет значение `DynamicRange`. При использовании запроса для получения исходных данных присоединитесь `?AdfRangePartitionUpbound` к предложению WHERE. Пример см. в разделе " [Параллельное копирование из Teradata](#parallel-copy-from-teradata) ". | Нет |
| партитионловербаунд | Минимальное значение столбца секционирования для копирования данных. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfRangePartitionLowbound` к предложению WHERE. Пример см. в разделе " [Параллельное копирование из Teradata](#parallel-copy-from-teradata) ". | Нет |

> [!NOTE]
>
> `RelationalSource`Тип источника копирования по-прежнему поддерживается, но не поддерживает новую встроенную параллельную загрузку из Teradata (параметры секционирования). Однако рекомендуется использовать новый набор данных.

**Пример. копирование данных с помощью простого запроса без секции**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Параллельная копия из Teradata

Соединитель Teradata фабрики данных предоставляет встроенное секционирование данных для копирования данных из Teradata в параллельном режиме. Параметры секционирования данных можно найти в **исходной** таблице действия копирования.

![Снимок экрана: параметры раздела](./media/connector-teradata/connector-teradata-partition-options.png)

При включении секционированной копии фабрика данных выполняет параллельные запросы к источнику Teradata для загрузки данных по секциям. Степень параллелизма определяется [`parallelCopies`](copy-activity-performance.md#parallel-copy) параметром действия копирования. Например, если установлено значение `parallelCopies` 4, фабрика данных одновременно создает и выполняет четыре запроса на основе указанного параметра секции и параметров, и каждый запрос извлекает часть данных из Teradata.

Рекомендуется включить параллельное копирование с секционированием данных, особенно при загрузке большого объема данных из Teradata. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных, основанное на файлах, оно передается в папку в виде нескольких файлов (указывается только имя папки). в этом случае производительность лучше, чем при записи в один файл.

| Сценарий                                                     | Предлагаемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная загрузка из большой таблицы.                                   | **Параметр секции**: Функции. <br><br/>Во время выполнения фабрика данных автоматически обнаруживает столбец PK, применяет к нему хэш и копирует данные по секциям. |
| Загрузка большого объема данных с помощью пользовательского запроса.                 | **Параметр секции**: Функции.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Столбец секционирования**: Укажите столбец, используемый для применения хэш-секции. Если не указано, фабрика данных автоматически обнаруживает столбец PK таблицы, указанной в наборе данных Teradata.<br><br>Во время выполнения фабрика данных `?AdfHashPartitionCondition` заменяет логику хэш-секции и отправляется в Teradata. |
| Загрузка большого объема данных с помощью пользовательского запроса, имеющего целочисленный столбец с равномерно распределенным значением для секционирования по диапазону. | **Параметры секции**: Динамический диапазон секций.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец секционирования**: Укажите столбец, используемый для секционирования данных. Можно секционировать по столбцу с целочисленным типом данных.<br>**Верхняя граница раздела** и **Нижняя граница секции**: Укажите, нужно ли выполнять фильтрацию по столбцу секционирования, чтобы получать данные только между нижним и верхним диапазонами.<br><br>Во время выполнения фабрика данных `?AdfRangePartitionColumnName`заменяет `?AdfRangePartitionUpbound`, и `?AdfRangePartitionLowbound` фактическим именем столбца и диапазонами значений для каждой секции и отправляется в Teradata. <br>Например, если для столбца секции "ID" задана Нижняя граница, равная 1, а верхняя граница — 80, а для параллельного копирования — значение 4, фабрика данных извлекает данные по 4 секциям. Их идентификаторы находятся в диапазоне от [1, 20], [21, 40], [41, 60] и [61, 80] соответственно. |

**Пример: запрос с хэш-секцией**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Пример: запрос с динамическим секционированным диапазоном**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Сопоставление типов для Teradata

При копировании данных из Teradata применяются следующие сопоставления. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Типы данных Teradata | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |Строковое |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Не поддерживается. Применить явное приведение в исходном запросе. |
| Целое число |Int32 |
| Interval Day |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Day To Hour |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Day To Minute |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Day To Second |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Hour |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Hour To Minute |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Hour To Second |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Minute |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Minute To Second |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Month |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Second |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Year |Не поддерживается. Применить явное приведение в исходном запросе. |
| Interval Year To Month |Не поддерживается. Применить явное приведение в исходном запросе. |
| Number |Double |
| Period (дата) |Не поддерживается. Применить явное приведение в исходном запросе. |
| Period (время) |Не поддерживается. Применить явное приведение в исходном запросе. |
| Period (время с часовым поясом) |Не поддерживается. Применить явное приведение в исходном запросе. |
| Период (метка времени) |Не поддерживается. Применить явное приведение в исходном запросе. |
| Период (метка времени с часовым поясом) |Не поддерживается. Применить явное приведение в исходном запросе. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Метка времени |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Не поддерживается. Применить явное приведение в исходном запросе. |
| Xml |Не поддерживается. Применить явное приведение в исходном запросе. |


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
