---
title: Копирование данных в Oracle и из нее с помощью Azure Data Factory
description: Узнайте, как копировать данные из поддерживаемых источников в базу данных Oracle или от Oracle до поддерживаемых магазинов приемников, используя Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 68e234b9db269c30dc9f24106ae1942c01304da7
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422503"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Копирование данных из Oracle и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-onprem-oracle-connector.md)
> * [Текущая версия](connector-oracle.md)

В этой статье описывается, как использовать активность копирования в Azure Data Factory для копирования данных из базы данных Oracle и в нее. Он основывается на [обзоре активности копирования.](copy-activity-overview.md)

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Oracle поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)

Вы можете скопировать данные из базы данных Oracle в любое хранилище данных, поддерживаемое в качестве приемника. Также можно скопировать данные из любого поддерживаемого хранилища данных в базу данных Oracle. Список хранилив данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В частности, этот разъем Oracle поддерживает:

- Следующие версии базы данных Oracle:
    - Oracle 18c R1 (18.1) и выше
    - Oracle 12c R1 (12.1) и выше
    - Oracle 11g R1 (11.1) и выше
    - Oracle 10g R1 (10.1) и выше
    - Oracle 9i R2 (9.2) и выше
    - Oracle 8i R3 (8.1.7) и выше
    - Служба облачных экзадданных Oracle
- Параллельное копирование из источника Oracle. Для получения подробной информации можно ознакомиться с [копией Параллельной информации из](#parallel-copy-from-oracle) раздела Oracle.

> [!Note]
> Прокси-сервер Oracle не поддерживается.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Среда выполнения интеграции содержит встроенный драйвер Oracle. Поэтому вам не нужно вручную устанавливать драйвер при копировании данных из базы данных Oracle и в нее.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Oracle.

## <a name="linked-service-properties"></a>Свойства связанной службы

Служба, связанная с Oracle, поддерживает следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено **на Oracle.** | Да |
| connectionString | Указывает сведения, необходимые для подключения к экземпляру базы данных Oracle. <br/>Вы также можете поместить пароль в Хранилище ключей Azure и вытащить конфигурацию `password` из строки соединения. С более подробной информацией обратитесь к следующим образцам и [учетным данным в Azure Key Vault.](store-credentials-in-key-vault.md) <br><br>**Поддерживаемые типы подключений**: вы можете использовать **ИД безопасности Oracle** или **имя службы Oracle** для идентификации базы данных.<br>— Если вы используете идентификатор безопасности, используйте этот код для подключения: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Если вы используете имя службы, используйте этот код: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Для продвинутых вариантов нативных подключений Oracle можно добавить запись в [TNSNAMES. Файл ORA](http://www.orafaq.com/wiki/Tnsnames.ora) на сервере Oracle и в связанном с ADF Oracle сервисе предпочитают использовать тип соединения Oracle Service Name и настроить соответствующее имя службы. | Да |
| connectVia | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

>[!TIP]
>Если вы получили ошибку, "ORA-01025: параметр UPI вне диапазона", `WireProtocolMode=1` и ваша версия Oracle 8i, добавьте к строке соединения. Затем повторите попытку.

Дополнительные свойства соединения, которые можно установить в строке соединения в вашем случае:

| Свойство | Описание | Допустимые значения |
|:--- |:--- |:--- |
| ArraySize |Количество байтов разъем может принести в одной сети туда и обратно. Например, `ArraySize=‭10485760‬`.<br/><br/>Большие значения увеличивают пропускную стоимость за счет сокращения количества раз для получения данных по сети. Меньшие значения увеличивают время отклика, так как задержки ожидания сервера для передачи данных меньше. | Несколько с 1 по 4294967296 (4 ГБ). Значение по умолчанию: `60000`. Значение 1 не определяет количество байтов, но указывает выделение места для точно одного ряда данных. |

Существует два варианта, чтобы включить шифрование для подключения Oracle:

-   Чтобы использовать **Triple-DES Шифрование (3DES) и Расширенный стандарт шифрования (AES) ,** на стороне сервера Oracle, перейдите на Oracle Advanced Security (OAS) и настроить настройки шифрования. Для получения подробной информации смотрите эту [документацию Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Разъем Oracle Application Development Framework (ADF) автоматически обсуждает метод шифрования, чтобы использовать тот, который вы настраиваете в OAS при установлении соединения с Oracle.

-   Для использования **SSL**:

    1.  Получите информацию о сертификате SSL. Получите Уважаемые правила кодирования (DER) закодированную информацию о сертификате вашего сертификата SSL, и сохранить выход (----- Начать сертификат ... End Certificate -----) как текстовый файл.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Пример:** Извлеките cert info от DERcert.cer, и после этого сохраните выход к cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Построить `keystore` `truststore`или . Следующая команда `truststore` создает файл, с или без пароля, в формате PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Пример:** Создайте файл PKCS12 `truststore` под названием MyTrustStoreFile с паролем.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Разместите `truststore` файл на самостоятельно размещенной ИК-машине. Например, поместите файл в C:'MyTrustStoreFile.
    4.  В Azure Data Factory настроите `EncryptionMethod=1` строку `TrustStore` / `TrustStorePassword`соединения Oracle и соответствующее значение. Например, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Примере:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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

В этом разделе содержится список свойств, поддерживаемых набором данных Oracle. Полный список разделов и свойств, доступных для определения наборов данных, [см.](concepts-datasets-linked-services.md) 

Для копирования данных от Oracle и в Oracle `OracleTable`установите свойство типа набора данных. Поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно `OracleTable`быть установлено. | Да |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Название таблицы/просмотра. |"Нет" для источника, "Да" для приемника  |
| tableName | Название таблицы/вид со схемой. Это свойство поддерживается для обратной совместимости. Для новой рабочей `schema` `table`нагрузки, использование и . | "Нет" для источника, "Да" для приемника |

**Примере:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником Oracle. Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle в качестве источника

>[!TIP]
>Для эффективной загрузки данных от Oracle с помощью раздела данных, узнайте больше из [параллельной копии от Oracle.](#parallel-copy-from-oracle)

Чтобы скопировать данные от Oracle, установите `OracleSource`тип исхода в активности копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования `OracleSource`должно быть установлено. | Да |
| oracleReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`.<br>При включании разделенной нагрузки необходимо подключить все соответствующие параметры встроенной перегородки в запросе. Например, смотрите [параллельную копию из](#parallel-copy-from-oracle) раздела Oracle. | Нет |
| разделыВарианты | Определяет параметры раздела данных, используемые для загрузки данных от Oracle. <br>Разрешенные значения: **Нет** (по умолчанию), **PhysicalPartitionsOfTable** и **DynamicRange**.<br>При включении параметра раздела (т.е. нет), `None`степень параллелизма для одновременной [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) загрузки данных из базы данных Oracle контролируется настройкой действия копирования. | Нет |
| разделНастройки | Укажите группу параметров для раздела данных. <br>Применить, когда параметр раздела `None`не является . | Нет |
| разделаИмена | Список физических разделов, которые необходимо скопировать. <br>Применить, когда параметр `PhysicalPartitionsOfTable`раздела . Если вы используете запрос для извлечения `?AdfTabularPartitionName` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из](#parallel-copy-from-oracle) раздела Oracle. | Нет |
| разделКоляНайм | Укажите имя исходного столбца **в типе целого ряда,** который будет использоваться разделом диапазона для параллельной копии. Если не указано, основной ключ таблицы автоматически обнаруживается и используется в качестве столбца раздела. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionColumnName` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из](#parallel-copy-from-oracle) раздела Oracle. | Нет |
| разделUpperBound | Максимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionUpbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из](#parallel-copy-from-oracle) раздела Oracle. | Нет |
| разделБолеей предел | Минимальное значение столбца раздела для копирования данных. <br>Применить, когда параметр `DynamicRange`раздела . Если вы используете запрос для извлечения `?AdfRangePartitionLowbound` исходных данных, подключите положение WHERE. Например, смотрите [параллельную копию из](#parallel-copy-from-oracle) раздела Oracle. | Нет |

**Пример: копирование данных с помощью базового запроса без раздела**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle в качестве приемника

Чтобы скопировать данные в Oracle, установите `OracleSink`тип раковины в активности копирования. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования `OracleSink`должно быть установлено. | Да |
| writeBatchSize | Вставляет данные в таблицу S'L, когда размер буфера достигает. `writeBatchSize`<br/>Допустимые значения: целое число (количество строк). |Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: временной диапазон. Например, 00:30:00 (30 минут). | Нет |
| preCopyScript | Укажите запрос на запуск копирования перед записью данных в Oracle в каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |

**Примере:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Параллельная копия от Oracle

Разъем Oracle Data Factory обеспечивает встроенное разъединение данных для параллельной копирования данных от Oracle. Параметры раздела данных можно найти на вкладке **«Источник»** действия копирования.

![Скриншот вариантов раздела](./media/connector-oracle/connector-oracle-partition-options.png)

При включании разделительной копии Data Factory выполняет параллельные запросы против источника Oracle для загрузки данных разделами. Параллельная степень контролируется настройкой [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) на активности копирования. Например, если `parallelCopies` вы устанавливаете четыре, Data Factory одновременно генерирует и запускает четыре запроса на основе указанного параметра раздела и настроек, и каждый запрос получает часть данных из базы данных Oracle.

Предлагается включить параллельную копию с разделом данных, особенно при загрузке большого объема данных из базы данных Oracle. Ниже приведены предлагаемые конфигурации для различных сценариев. При копировании данных в хранилище данных на основе файлов рекомендуется записывать в папку в виде нескольких файлов (только указать имя папки), и в этом случае производительность лучше, чем запись в один файл.

| Сценарий                                                     | Рекомендуемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная нагрузка с большого стола, с физическими перегородками.          | **Вариант раздела**: Физические разделы таблицы. <br><br/>Во время выполнения Data Factory автоматически обнаруживает физические разделы и копирует данные по разделам. |
| Полная нагрузка из большой таблицы, без физических разделов, в то время как с целым столбцом для раздела данных. | **Параметры раздела**: Динамический раздел диапазона.<br>**Столбец раздела**: Указать столбец, используемый для данных раздела. Если не указано, используется основной столбец ключа. |
| Загрузите большой объем данных с помощью пользовательского запроса с физическими перегородками. | **Вариант раздела**: Физические разделы таблицы.<br>**Запрос**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Имя раздела**: Укажите имя раздела (ы) для копирования данных. Если не указано, Data Factory автоматически обнаруживает физические разделы на таблице, указанной в наборе данных Oracle.<br><br>Во время выполнения Фабрика данных заменяется `?AdfTabularPartitionName` фактическим именем раздела и отправляется в Oracle. |
| Загрузите большой объем данных, используя пользовательский запрос, без физических разделов, в то время как с рядовой столбец для раздела данных. | **Параметры раздела**: Динамический раздел диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец раздела**: Указать столбец, используемый для данных раздела. Можно разгородиться по столбце с типом данных с разреза.<br>**Раздел верхней границы** и **раздел нижней границы**: Укажите, хотите ли вы отфильтровать столбец раздела для получения данных только между нижним и верхним диапазоном.<br><br>Во время выполнения, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` заменяет , и с фактическим именем столбца и диапазонов значений для каждого раздела, и отправляет Oracle. <br>Например, если столбец раздела "ID" установлен с нижней полосой как 1, а верхней - 80, с параллельным набором копий как 4, Data Factory получает данные четырёми разделами. Их идентионы находятся между 1,20, 21, 40, 41, 60 и 61, 80, соответственно. |

**Пример: запрос с физическим перегородкой**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Пример: запрос с динамическим перегородкой диапазона**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Сопоставление типов данных для Oracle

При копировании данных от Oracle и в Oracle применяются следующие отображения. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Oracle | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(поддерживается только в Oracle 10g и более поздних версий) |
| CHAR |Строка |
| CLOB |Строка |
| DATE |Дата и время |
| FLOAT |десятичное число, строка (если точность больше 28) |
| INTEGER |десятичное число, строка (если точность больше 28) |
| LONG |Строка |
| LONG RAW |Byte[] |
| NCHAR |Строка |
| NCLOB |Строка |
| NUMBER |десятичное число, строка (если точность больше 28) |
| NVARCHAR2 |Строка |
| RAW |Byte[] |
| ROWID |Строка |
| timestamp |Дата и время |
| TIMESTAMP WITH LOCAL TIME ZONE |Строка |
| TIMESTAMP WITH TIME ZONE |Строка |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Строка |
| XML |Строка |

> [!NOTE]
> Типы данных INTERVAL YEAR TO MONTH и INTERVAL DAY TO SECOND не поддерживаются.

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
