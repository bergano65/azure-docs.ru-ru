---
title: Копирование данных от SAP HANA
description: Узнайте, как копировать данные из SAP HANA на поддерживаемые приемники хранилища данных с помощью действия копирования в конвейере фабрики данных Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265887"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Копирование данных из SAP HANA с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-sap-hana-connector.md)
> * [Текущая версия](connector-sap-hana.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных SAP HANA. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

>[!TIP]
>Чтобы узнать общую поддержку ADF по сценарию интеграции данных SAP, см. [интеграцию данных SAP с помощью whitepaper Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) с подробным введением, компарсом и руководством.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем SAP HANA поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Данные из базы данных SAP HANA можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SAP HANA поддерживает:

- Копирование данных из всех версий базы данных SAP HANA.
- Копирование данных из **информационных моделей HANA** (таких как аналитические и расчетные представления) и **строки/колонки таблицы**.
- копирование данных с использованием **базовой** проверки подлинности или проверки подлинности **Windows**.
- Параллельное копирование из источника SAP HANA. Для получения подробной информации можно ознакомиться с [параллельной копией раздела SAP HANA.](#parallel-copy-from-sap-hana)

> [!TIP]
> Чтобы скопировать данные **в** хранилище данных SAP HANA, используйте универсальный соединитель ODBC. Подробные сведения см. в разделе о [приемнике SAP HANA](connector-odbc.md#sap-hana-sink). Обратите внимание, что связанные службы для соединителя SAP HANA и соединитель ODBC нельзя использовать повторно, так как они имеют разные типы.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать этот соединитель SAP HANA, сделайте следующее:

- Настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md).
- Установите на компьютере среды выполнения интеграции драйвер ODBC SAP HANA. Драйвер ODBC для SAP HANA можно скачать на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Выполните поиск по ключевой фразе **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю SAP HANA.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SAP HANA поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SapHana** | Да |
| connectionString | Укажите информацию, необходимую для подключения к SAP HANA, используя либо **базовую аутентификацию,** либо **проверку подлинности Windows.** Ознакомьтесь с приведенными ниже примерами.<br>В связи строка сервера/порта является обязательной (порт по умолчанию 30015), а имя пользователя и пароль обязательны при использовании базовой аутентификации. Для получения дополнительных расширенных параметров обратитесь к [SAP HANA ODBC Connection Properties](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Вы также можете поместить пароль в Хранилище ключей Azure и вытащить конфигурацию пароля из строки соединения. С более подробной информацией обратитесь к учетным данным Магазина в статье [Azure Key Vault.](store-credentials-in-key-vault.md) | Да |
| userName | Укажите имя пользователя при использовании аутентификации Windows. Например, `user@domain.com`. | нет |
| password | Укажите пароль для учетной записи пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Требуется локальная среда IR, как упоминалось в разделе [Предварительные требования](#prerequisites). |Да |

**Пример: использование базовой аутентификации**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример: использование аутентификации Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Если вы использовали службу SAP HANA, связанную со следующей полезной нагрузкой, она по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

**Примере:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе содержится список свойств, поддерживаемых набором данных SAP HANA.

Для копирования данных sAP HANA поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на: **SapHanaTable** | Да |
| схема | Название схемы в базе данных SAP HANA. | Нет (если свойство query указано в источнике действия) |
| table | Название таблицы в базе данных SAP HANA. | Нет (если свойство query указано в источнике действия) |

**Примере:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Если вы `RelationalTable` использовали набор набранных данных, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA в качестве источника

>[!TIP]
>Чтобы эффективно глотать данные от SAP HANA с помощью раздела данных, узнайте больше из [параллельной копии из раздела SAP HANA.](#parallel-copy-from-sap-hana)

Для копирования данных из SAP HANA в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено: **SapHanaSource** | Да |
| query | Указывает SQL-запрос для чтения данных из экземпляра SAP HANA. | Да |
| разделыВарианты | Определяет параметры раздела данных, используемые для приема данных из SAP HANA. Узнайте больше из [параллельной копии из раздела SAP HANA.](#parallel-copy-from-sap-hana)<br>Допустимые значения: **Нет** (по умолчанию), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Узнайте больше из [параллельной копии из раздела SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`может использоваться только при копировании данных из таблицы, но не при запросе. <br>При включении параметра раздела (т.е. нет), `None`степень параллелизма для одновременной [`parallelCopies`](copy-activity-performance.md#parallel-copy) загрузки данных из SAP HANA контролируется параметром на активности копирования. | False |
| разделНастройки | Укажите группу параметров для раздела данных.<br>Применить, когда параметр `SapHanaDynamicRange`раздела . | False |
| разделКоляНайм | Укажите имя исходной столбца, который будет использоваться разделом для параллельной копии. Если не указано, индекс или основной ключ таблицы автоматически обнаруживается и используется в качестве столбца раздела.<br>Применить, когда параметр `SapHanaDynamicRange`раздела . Если вы используете запрос для извлечения `?AdfHanaDynamicRangePartitionCondition` исходных данных, подключите положение WHERE. Смотрите пример в [параллельной копии из раздела SAP HANA.](#parallel-copy-from-sap-hana) | Да при `SapHanaDynamicRange` использовании раздела. |
| packetSize | Определяет размер сетевого пакета (в килобаитах) для разделения данных на несколько блоков. Если у вас есть большой объем данных для копирования, увеличение размера пакета может увеличить скорость чтения от SAP HANA в большинстве случаев. Тестирование производительности рекомендуется при корректировке размера пакета. | Нет.<br>Значение по умолчанию 2048 (2 МБ). |

**Примере:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Если вы `RelationalSource` использовали набранный источник копий, он по-прежнему поддерживается как есть, в то время как вам предлагается использовать новый в будущем.

## <a name="parallel-copy-from-sap-hana"></a>Параллельная копия от SAP HANA

Разъем SAP HANA Data Factory обеспечивает параллельное встроенное разъединение данных для копирования данных с SAP HANA. Параметры раздела данных можно найти в таблице **«Источник»** действия копирования.

![Скриншот вариантов раздела](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

При включании раздела копии Data Factory выполняет параллельные запросы против вашего источника SAP HANA для получения данных по разделам. Параллельная степень контролируется настройкой [`parallelCopies`](copy-activity-performance.md#parallel-copy) на активности копирования. Например, если `parallelCopies` вы устанавливаете четыре, Data Factory одновременно генерирует и запускает четыре запроса на основе указанного параметра раздела и настроек, и каждый запрос получает часть данных из SAP HANA.

Предлагается включить параллельную копию с разделом данных, особенно при поимке большого объема данных из SAP HANA. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных, основанных на файлах, рекомендуется записывать в папку в виде нескольких файлов (только указать имя папки), и в этом случае производительность лучше, чем запись в один файл.

| Сценарий                                           | Рекомендуемые параметры                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Полная нагрузка с большого стола.                        | **Вариант раздела**: Физические разделы таблицы. <br><br/>Во время выполнения Data Factory автоматически обнаруживает физический тип раздела указанной таблицы SAP HANA и выбирает соответствующую стратегию раздела:<br>- **Раздел диапазона**: Получить столбец раздела и диапазоны разделов, определенные для таблицы, затем скопировать данные по диапазону. <br>- **Хэш Раздел:** Используйте ключ раздела хэша в качестве столбца раздела, затем раздел и копируйте данные на основе вычисленных диапазонов ADF. <br>- **Раздел раунда или** отсутствие **раздела**: Использование основного ключа в качестве столбца раздела, затем раздел и копирование данных на основе вычисляемых диапазонов ADF. |
| Загрузите большой объем данных с помощью пользовательского запроса. | **Вариант раздела**: Динамический раздел диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Столбец раздела**: Указать столбец, используемый для применения динамического раздела диапазона. <br><br>Во время выполнения Data Factory сначала вычисляет диапазоны значений указанного столбца раздела, равномерно распределяя строки в число ведер в зависимости от количества `?AdfHanaDynamicRangePartitionCondition` различных значений столбца раздела и параллельной настройки параллельной копии ADF, а затем заменяет фильтрацию диапазона значений столба раздела для каждого раздела и отправляется в SAP HANA.<br><br>Если вы хотите использовать несколько столбцов в качестве столбца раздела, можно спроектировать значения каждого столбца в виде одного столбца в запросе и указать его в качестве столбца раздела в ADF, например. `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` |

**Пример: запрос с физическими перегородками таблицы**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Пример: запрос с динамическим перегородкой диапазона**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Сопоставление типов данных для SAP HANA

При копировании данных из SAP HANA используются следующие сопоставления типов данных SAP HANA с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SAP HANA | Тип промежуточных данных фабрики данных |
| ------------------ | ------------------------------ |
| ALPHANUM           | Строка                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| БИНТЕКСТ            | Строка                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | Строка                         |
| DATE               | Дата и время                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | Строка                         |
| NVARCHAR           | Строка                         |
| real               | Один                         |
| SECONDDATE         | Дата и время                       |
| ШОРТТЕКСТ          | Строка                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| СХКХЕРАЗИТИТИ     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | Строка                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Строка                         |
| timestamp          | Дата и время                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
