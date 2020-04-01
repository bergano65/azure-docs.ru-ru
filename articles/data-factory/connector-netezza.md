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
ms.openlocfilehash: c7e17f7c4493560bd6118b8d4837fd795a6ab0c8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422858"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Копирование данных из Netezza с помощью Фабрики данных Azure

Из этой статьи вы узнаете, как с помощью действия копирования в Фабрике данных Azure копировать данные из Netezza. Это продолжение статьи о [действии копирования в Фабрике данных Azure](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!TIP]
>В сценарии миграции данных из Netezza в Azure можно узнать больше от [Группы данных Useure data Factory для переноса данных с сервера Netezza в Azure.](data-migration-guidance-netezza-azure-sqldw.md)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Netezza поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)


Данные из Netezza можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, поддерживаемых действием копирования в качестве источников и приемников, приведен в разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Разъем Netezza поддерживает параллельное копирование из источника. Для получения подробной информации из раздела [Netezza смотрите параллельную копию раздела Netezza.](#parallel-copy-from-netezza)

В Фабрике данных Azure предоставляется встроенный драйвер, который обеспечивает подключение. Не нужно вручную устанавливать драйвер для использования этого соединителя.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

Конвейер можно создать с помощью действия копирования и пакета SDK для .NET, пакета SDK для Python, Azure PowerShell, REST API либо шаблона Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей Фабрики данных, относящихся к соединителю Netezza.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Netezza поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **Netezza**. | Да |
| connectionString | Строка для подключения к Netezza через интерфейс ODBC. <br/>Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `pwd` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. В следующей таблице описаны дополнительные свойства, которые можно задать.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| SecurityLevel | Уровень безопасности (SSL/TLS), который драйвер использует для подключения к хранилищу данных. Например, `SecurityLevel=preferredSecured`. Поддерживаются значения:<br/>- **Только незащищенный** (**onlyUnSecured**): драйвер не использует SSL.<br/>- **Предпочтительно незащищенный (preferredUnSecured) (по умолчанию)**: если сервер предоставляет возможность выбора, драйвер не использует SSL. <br/>- **Предпочтительный защищенный (предпочтительныйБезопасный)**: Если сервер предоставляет выбор, драйвер использует SSL. <br/>- **Только защищенный (onlySecured)**: драйвер не подключается, если SSL-подключение недоступно. | Нет |
| CaCertFile | Полный путь к SSL-сертификату, используемому сервером. Пример: `CaCertFile=<cert path>;`| Да, если протокол SSL включен |

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

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено: **NetezzaTable** | Да |
| схема | Имя схемы. |Нет (если свойство query указано в источнике действия)  |
| table | Имя таблицы. |Нет (если свойство query указано в источнике действия)  |
| tableName | Название стола со схемой. Это свойство поддерживается для обратной совместимости. Использовать `schema` `table` и для новой рабочей нагрузки. | Нет (если свойство query указано в источнике действия) |

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
>Для эффективной загрузки данных из Netezza с помощью раздела данных, узнайте больше из [параллельной копии из раздела Netezza.](#parallel-copy-from-netezza)

Чтобы копировать данные из Netezza, установите для типа **source** в действии копирования значение **NetezzaSource**. Следующие свойства поддерживаются в **разделе источник** копирования активности:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно иметь значение **NetezzaSource**. | Да |
| query | Используйте пользовательский SQL-запрос для чтения данных. Пример: `"SELECT * FROM MyTable"` | Нет (если для набора данных задано свойство tableName) |
| разделыВарианты | Определяет параметры раздела данных, используемые для загрузки данных из Netezza. <br>Допустимые значения: **Нет** (по умолчанию), **DataSlice**и **DynamicRange**.<br>При включении параметра раздела (т.е. нет), `None`степень параллелизма для одновременной [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) загрузки данных из базы данных Netezza контролируется путем установки на активность копирования. | Нет |
| разделНастройки | Укажите группу параметров для раздела данных. <br>Применить, когда параметр раздела не `None`является . | Нет |
| разделКоляНайм | Укажите имя исходного столбца **в типе целого ряда,** который будет использоваться разделом диапазона для параллельной копии. Если не указано, основной ключ таблицы автоматически обнаруживается и используется в качестве столбца раздела. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionColumnName` исходных данных, подключите положение WHERE. Смотрите пример в [параллельной копии из раздела Netezza.](#parallel-copy-from-netezza) | Нет |
| разделUpperBound | Максимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для получения `?AdfRangePartitionUpbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из раздела Netezza.](#parallel-copy-from-netezza) | Нет |
| разделБолеей предел | Минимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionLowbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из раздела Netezza.](#parallel-copy-from-netezza) | Нет |

**Примере:**

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

## <a name="parallel-copy-from-netezza"></a>Параллельная копия от Netezza

Разъем Data Factory Netezza обеспечивает встроенное разъединение данных для параллельной копирования данных из Netezza. Параметры раздела данных можно найти в таблице **«Источник»** действия копирования.

![Скриншот вариантов раздела](./media/connector-netezza/connector-netezza-partition-options.png)

При включании раздела копии Data Factory выполняет параллельные запросы против источника Netezza для загрузки данных разделами. Параллельная степень контролируется настройкой [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) на активности копирования. Например, если `parallelCopies` вы устанавливаете четыре, Data Factory одновременно генерирует и запускает четыре запроса на основе указанного параметра раздела и настроек, и каждый запрос извлекает часть данных из базы данных Netezza.

Предлагается включить параллельную копию с разделом данных, особенно при загрузке большого объема данных из базы данных Netezza. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных на основе файлов рекомендуется записывать в папку в виде нескольких файлов (только указать имя папки), и в этом случае производительность лучше, чем запись в один файл.

| Сценарий                                                     | Рекомендуемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная нагрузка с большого стола.                                   | **Вариант раздела**: Фрагмент данных. <br><br/>Во время выполнения Data Factory автоматически перерезает данные на основе [встроенных срезов данных Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)и копирует данные по разделам. |
| Загрузите большой объем данных с помощью пользовательского запроса.                 | **Вариант раздела**: Фрагмент данных.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Во время выполнения, `?AdfPartitionCount` Фабрика данных заменяет (с `?AdfDataSliceCondition` параллельным номером копирования, установленным на активности копирования) и с логикой раздела раздела данных, и отправляет netezza. |
| Загрузите большой объем данных с помощью пользовательского запроса, имея целый столбец с равномерно распределенным значением для раздела диапазона. | **Параметры раздела**: Динамический раздел диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец раздела**: Указать столбец, используемый для данных раздела. Можно разгородиться по столбце с типом данных с разреза.<br>**Раздел верхней границы** и **раздел нижней границы**: Укажите, хотите ли вы отфильтровать столбец раздела для получения данных только между нижним и верхним диапазоном.<br><br>Во время выполнения, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` заменяет , и с фактическим именем столбца и диапазонов значений для каждого раздела, и отправляет netezza. <br>Например, если столбец раздела "ID" установлен с нижней полосой как 1, а верхний - 80, с параллельным набором копий как 4, Data Factory получает данные четырёми разделами. Их идентионы находятся между 1,20, 21, 40, 41, 60 и 61, 80, соответственно. |

**Пример: запрос с разделом фрагмента данных**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Пример: запрос с динамическим перегородкой диапазона**

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

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Следующие шаги

В разделе [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure.
