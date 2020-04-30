---
title: Копирование данных из Netezza с помощью Фабрики данных Azure
description: Узнайте, как копировать данные из Netezza в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 89efa8dc9989f693964415741299042c63f93780
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418122"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Копирование данных из Netezza с помощью Фабрики данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные из Netezza. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!TIP]
>Сведения о сценарии переноса данных из Netezza в Azure см. в статье [Использование фабрики данных Azure для переноса данных с локального сервера Netezza в Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Netezza поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)


Данные из Netezza можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Соединитель Netezza поддерживает параллельное копирование из источника. Дополнительные сведения см. в разделе [Параллельное копирование из Netezza](#parallel-copy-from-netezza) .

В Фабрике данных Azure предоставляется встроенный драйвер, который обеспечивает подключение. Не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="prerequisites"></a>Предварительные условия

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

Конвейер можно создать с помощью действия копирования и пакета SDK для .NET, пакета SDK для Python, Azure PowerShell, REST API либо шаблона Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Netezza.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Netezza поддерживаются следующие свойства.

| Свойство | Описание | Обязательный |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **Netezza**. | Да |
| connectionString | Строка для подключения к Netezza через интерфейс ODBC. <br/>Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `pwd` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. В следующей таблице описаны дополнительные свойства, которые можно задать.

| Свойство | Описание | Обязательный |
|:--- |:--- |:--- |
| SecurityLevel | Уровень безопасности (SSL/TLS), который драйвер использует для подключения к хранилищу данных. Например, `SecurityLevel=preferredSecured`. Поддерживаются значения:<br/>- **Только незащищенный** (**онлюнсекуред**): драйвер не использует TLS.<br/>- **Предпочтительный незащищенный (преферредунсекуред) (по умолчанию)**. Если сервер предоставляет возможность, драйвер не использует TLS. <br/>- **Предпочитаемая защита (преферредсекуред)**: Если сервер предоставляет возможность выбора, драйвер использует TLS. <br/>- **Только защищенный (онлисекуред)**. драйвер не подключается, если не доступно TLS-подключение. | Нет |
| CaCertFile | Полный путь к сертификату TLS/SSL, используемому сервером. Пример: `CaCertFile=<cert path>;`| Да, если TLS включен |

**Пример**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: хранение пароля в Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Этот раздел содержит список свойств, поддерживаемых набором данных Netezza.

Полный список разделов и свойств, используемых для определения наборов данных, приведен в статье [Наборы данных и связанные службы в фабрике данных Azure](concepts-datasets-linked-services.md).

Чтобы скопировать данные из Netezza, установите для свойства **type** набора данных значение **NetezzaTable**. Поддерживаются следующие свойства:

| Свойство | Описание | Обязательный |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **нетеззатабле** . | Да |
| схема | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Имя таблицы со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Используйте `schema` и `table` для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником Netezza.

Полный список разделов и свойств, доступных для определения действий, см. в статье, посвященной [конвейерам и действиям в Фабрике данных Azure](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza в качестве источника

>[!TIP]
>Чтобы эффективно загружать данные из Netezza с помощью секционирования данных, изучите дополнительные сведения из раздела [Параллельное копирование из Netezza](#parallel-copy-from-netezza) .

Чтобы копировать данные из Netezza, установите для типа **source** в действии копирования значение **NetezzaSource**. В разделе **источник** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательный |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно иметь значение **NetezzaSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Пример: `"SELECT * FROM MyTable"` | Нет (если для набора данных задано свойство tableName) |
| партитионоптионс | Задает параметры секционирования данных, используемые для загрузки данных из Netezza. <br>Допустимые значения: **None** (по умолчанию), **slice**и **динамикранже**.<br>Если параметр секции включен (то есть не `None`), степень параллелизма для параллельной загрузки данных из базы данных Netezza управляется [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) параметром в действии копирования. | Нет |
| партитионсеттингс | Укажите группу параметров для секционирования данных. <br>Применить, если параметр секционирования не `None`имеет значение. | Нет |
| партитионколумннаме | Укажите имя исходного столбца **в целочисленном типе** , который будет использоваться секционированием по диапазонам для параллельного копирования. Если не указано, первичный ключ таблицы автоматически определяется и используется в качестве столбца секционирования. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. При использовании запроса для получения исходных данных присоединитесь `?AdfRangePartitionColumnName` к предложению WHERE. См. пример в разделе [Параллельное копирование из Netezza](#parallel-copy-from-netezza) . | Нет |
| партитионуппербаунд | Максимальное значение столбца секционирования для копирования данных. <br>Применить, если параметр секционирования имеет значение `DynamicRange`. При использовании запроса для получения исходных данных присоединитесь `?AdfRangePartitionUpbound` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Netezza](#parallel-copy-from-netezza) . | Нет |
| партитионловербаунд | Минимальное значение столбца секционирования для копирования данных. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfRangePartitionLowbound` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Netezza](#parallel-copy-from-netezza) . | Нет |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Параллельное копирование из Netezza

Соединитель Netezza фабрики данных предоставляет встроенное секционирование данных для параллельного копирования данных из Netezza. Параметры секционирования данных можно найти в **исходной** таблице действия копирования.

![Снимок экрана: параметры раздела](./media/connector-netezza/connector-netezza-partition-options.png)

При включении секционированной копии фабрика данных выполняет параллельные запросы к источнику Netezza для загрузки данных по секциям. Степень параллелизма определяется [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) параметром действия копирования. Например, если установлено значение `parallelCopies` 4, фабрика данных одновременно создает и выполняет четыре запроса на основе указанного параметра секции и параметров, и каждый запрос извлекает часть данных из базы данных Netezza.

Рекомендуется включить параллельное копирование с секционированием данных, особенно при загрузке большого объема данных из базы данных Netezza. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных, основанное на файлах, оно передается в папку в виде нескольких файлов (указывается только имя папки). в этом случае производительность лучше, чем при записи в один файл.

| Сценарий                                                     | Рекомендуемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная загрузка из большой таблицы.                                   | **Параметр секции**: срез данных. <br><br/>Во время выполнения фабрика данных автоматически разделяет данные на основе [встроенных срезов данных Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)и копирует данные по секциям. |
| Загрузка большого объема данных с помощью пользовательского запроса.                 | **Параметр секции**: срез данных.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Во время выполнения фабрика данных `?AdfPartitionCount` заменяет (с параллельным числом копий, заданным `?AdfDataSliceCondition` в действии копирования) и с логикой секционирования данных и отправляется в Netezza. |
| Загрузка большого объема данных с помощью пользовательского запроса, имеющего целочисленный столбец с равномерно распределенным значением для секционирования по диапазону. | **Параметры секции**: Динамическая секция диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец секционирования**: укажите столбец, используемый для секционирования данных. Можно секционировать по столбцу с целочисленным типом данных.<br>**Верхняя граница секции** и **Нижняя граница секции**: укажите, следует ли фильтровать столбец секционирования, чтобы получать данные только между нижним и верхним диапазонами.<br><br>Во время выполнения фабрика данных `?AdfRangePartitionColumnName`заменяет `?AdfRangePartitionUpbound`, и `?AdfRangePartitionLowbound` фактическим именем столбца и диапазонами значений для каждой секции и отправляется в Netezza. <br>Например, если для столбца секции "ID" задана Нижняя граница, равная 1, а верхняя граница — 80, а для параллельного копирования — значение 4, фабрика данных извлекает данные по 4 секциям. Их идентификаторы находятся в диапазоне от [1, 20], [21, 40], [41, 60] и [61, 80] соответственно. |

**Пример: запрос с Секцией среза данных**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Пример: запрос с динамическим секционированным диапазоном**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Дальнейшие шаги

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
