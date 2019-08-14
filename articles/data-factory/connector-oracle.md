---
title: Копирование данных в базу данных Oracle и из нее с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ в базу данных Oracle или из Oracle в поддерживаемые хранилища приемников с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 142c99b2471a9010a00bf9b5d50549c5e84548f1
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966464"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Копирование данных из Oracle и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
> * [Версия 1](v1/data-factory-onprem-oracle-connector.md)
> * [Текущая версия](connector-oracle.md)

В этой статье описано, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных Oracle и в нее. Она основана на [обзоре действия копирования](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из базы данных Oracle в любое хранилище данных, поддерживаемое в качестве приемника. Также можно скопировать данные из любого поддерживаемого хранилища данных в базу данных Oracle. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель Oracle поддерживает:

- Следующие версии базы данных Oracle:
    - Oracle 18C R1 (18,1) и выше
    - Oracle 12c R1 (12,1) и выше
    - Oracle 11g R1 (11,1) и выше
    - Oracle 10G R1 (10,1) и более поздних версий
    - Oracle 9i R2 (9,2) и более поздние версии
    - Oracle 8i R3 (8.1.7) и более поздние версии
    - Служба Oracle Database Cloud Ексадата
- Копирование данных с помощью проверки подлинности Basic или OID.
- Параллельное копирование из источника Oracle. Дополнительные сведения см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Прокси-сервер Oracle не поддерживается.

## <a name="prerequisites"></a>предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Среда выполнения интеграции содержит встроенный драйвер Oracle. Поэтому вам не нужно вручную устанавливать драйвер при копировании данных из базы данных Oracle и в нее.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Oracle.

## <a name="linked-service-properties"></a>Свойства связанной службы

Связанная служба Oracle поддерживает следующие свойства:

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Oracle**. | Да |
| connectionString | Указывает сведения, необходимые для подключения к экземпляру базы данных Oracle. <br/>Пометьте это поле как `SecureString` , чтобы безопасно хранить его в фабрике данных. Можно также добавить пароль в Azure Key Vault и `password` извлечь конфигурацию из строки подключения. Ознакомьтесь со следующими примерами и [Храните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md) с дополнительными сведениями. <br><br>**Поддерживаемые типы подключений**: вы можете использовать **SID-идентификатор Oracle** или **имя службы Oracle** для идентификации базы данных.<br>— Если вы используете идентификатор безопасности, используйте этот код для подключения: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Если вы используете имя службы, используйте этот код: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Да |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если не указано другое, по умолчанию используется интегрированная Azure Integration Runtime. |Нет |

>[!TIP]
>При возникновении ошибки "ORA-01025: Параметр УПИ вне допустимого диапазона ", а версия Oracle — 8i, добавьте `WireProtocolMode=1` в строку подключения. Затем повторите попытку.

Существует два варианта, чтобы включить шифрование для подключения Oracle:

-   Чтобы использовать **Шифрование Triple-DES (3DES) и AES (AES)** на стороне сервера Oracle, перейдите в раздел Oracle Advanced Security (OAS) и настройте параметры шифрования. Дополнительные сведения см. в этой [документации Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Соединитель платформы Oracle Application Development Framework (ADF) автоматически согласовывает метод шифрования для использования того, который вы настроили в OAS при установлении соединения с Oracle.

-   Чтобы использовать **SSL**, выполните следующие действия.

    1.  Получите сведения о сертификате SSL. Получите сведения о сертификате SSL в кодировке Distinguished Encoding Rules (DER) и сохраните выходные данные (-----Begin Certificate... End Certificate -----) как текстовый файл.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Пример.** Извлеките сведения о сертификате из Дерцерт. cer и сохраните выходные данные в CERT. txt.

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
    
    2.  `keystore` Создайте или .`truststore` Следующая команда создает `truststore` файл с паролем в формате PKCS-12 или без него.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Пример.** Создайте файл PKCS12 `truststore` с именем митрустсторефиле и паролем.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  `truststore` Поместите файл на автономный IR-компьютер. Например, поместите файл по адресу К:\митрустсторефиле.
    4.  В фабрике данных Azure настройте строку подключения Oracle с помощью `EncryptionMethod=1` и соответствующего `TrustStore` / `TrustStorePassword`значения. Например, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Пример.**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
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

В этом разделе содержится список свойств, поддерживаемых набором данных Oracle. Полный список разделов и свойств, доступных для определения наборов данных, см. в разделе [наборы данных](concepts-datasets-linked-services.md). 

Чтобы скопировать данные из и в Oracle, присвойте свойству Type набора данных `OracleTable`значение. Поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение `OracleTable`. | Да |
| tableName |Имя таблицы в базе данных Oracle, на которое ссылается связанная служба. | Да |

**Пример.**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Этот раздел содержит список свойств, поддерживаемых источником и приемником Oracle. Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle в качестве источника

>[!TIP]
>Чтобы эффективно загружать данные из Oracle с помощью секционирования данных, см. раздел [параллельная копия из Oracle](#parallel-copy-from-oracle).

Чтобы скопировать данные из Oracle, задайте тип источника в действии `OracleSource`копирования. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type источника действия копирования должно иметь значение `OracleSource`. | Да |
| oracleReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`.<br>При включении секционированной нагрузки необходимо привязать все соответствующие встроенные параметры секции в запросе. Примеры см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) . | Нет |
| partitionOptions | Задает параметры секционирования данных, используемые для загрузки данных из Oracle. <br>Допустимые значения: **Нет** (по умолчанию), **фисикалпартитионсофтабле** и **динамикранже**.<br>Если параметр секции включен (то есть не `None`), также [`parallelCopies`](copy-activity-performance.md#parallel-copy) настройте параметр для действия копирования. Это определяет параллельную степень для одновременной загрузки данных из базы данных Oracle. Например, вы можете задать для этого параметра значение 4. | Нет |
| partitionSettings | Укажите группу параметров для секционирования данных. <br>Применяется, если параметр Partition не `None`имеет значение. | Нет |
| partitionNames | Список физических разделов, которые необходимо скопировать. <br>Применяется, если параметр Partition имеет `PhysicalPartitionsOfTable`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfTabularPartitionName` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) . | Нет |
| partitionColumnName | Укажите имя исходного столбца **в целочисленном типе** , который будет использоваться секционированием по диапазонам для параллельного копирования. Если этот параметр не указан, первичный ключ таблицы определяется автоопределением и используется в качестве столбца секционирования. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfRangePartitionColumnName` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) . | Нет |
| partitionUpperBound | Максимальное значение столбца секционирования для копирования данных. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfRangePartitionUpbound` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) . | Нет |
| партитионловербаунд | Минимальное значение столбца секционирования для копирования данных. <br>Применяется, если параметр Partition имеет `DynamicRange`значение. Если для получения исходных данных используется запрос, присоединитесь `?AdfRangePartitionLowbound` к предложению WHERE. Пример см. в разделе [Параллельное копирование из Oracle](#parallel-copy-from-oracle) . | Нет |

**Пример. копирование данных с помощью простого запроса без секции**

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

Чтобы скопировать данные в Oracle, задайте для `OracleSink`типа приемника в действии копирования значение. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type приемника действия копирования должно иметь значение `OracleSink`. | Да |
| writeBatchSize | Вставляет данные в таблицу SQL по достижении `writeBatchSize`размера буфера.<br/>Допустимые значения: целое число (количество строк). |Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: временной диапазон. Например, 00:30:00 (30 минут). | Нет |
| preCopyScript | Укажите SQL-запрос для выполнения действия копирования, прежде чем записывать данные в Oracle при каждом запуске. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |

**Пример.**

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

## <a name="parallel-copy-from-oracle"></a>Параллельная копия из Oracle

Соединитель Oracle фабрики данных предоставляет встроенное секционирование данных для параллельного копирования данных из Oracle. Параметры секционирования данных можно найти на вкладке **источник** действия копирования.

![Снимок экрана: параметры раздела](./media/connector-oracle/connector-oracle-partition-options.png)

При включении секционированной копии фабрика данных выполняет параллельные запросы к источнику Oracle для загрузки данных по секциям. Степень параллелизма определяется [`parallelCopies`](copy-activity-performance.md#parallel-copy) параметром действия копирования. Например, если установлено значение `parallelCopies` 4, фабрика данных одновременно создает и выполняет четыре запроса на основе указанного параметра секции и параметров. Каждый запрос извлекает часть данных из базы данных Oracle.

Рекомендуется включить параллельное копирование с секционированием данных, особенно при загрузке большого объема данных из базы данных Oracle. Ниже приведены предлагаемые конфигурации для различных сценариев.

| Сценарий                                                     | Предлагаемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная загрузка из большой таблицы с физическими секциями.          | **Параметр секции**: Физические разделы таблицы. <br><br/>Во время выполнения фабрика данных автоматически обнаруживает физические секции и копирует данные по секциям. |
| Полная загрузка из большой таблицы без физических секций с целочисленным столбцом для секционирования данных. | **Параметры секции**: Динамический диапазон секций.<br>**Столбец секционирования**: Укажите столбец, используемый для секционирования данных. Если не указано, используется первичный ключевой столбец. |
| Загрузка большого объема данных с помощью пользовательского запроса с физическими секциями. | **Параметр секции**: Физические разделы таблицы.<br>**Запрос**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Имя секции**: Укажите имена разделов, из которых следует копировать данные. Если не указано, фабрика данных автоматически обнаруживает физические секции в таблице, указанной в наборе данных Oracle.<br><br>Во время выполнения фабрика данных `?AdfTabularPartitionName` заменяет фактическое имя секции и отправляется в Oracle. |
| Загрузка большого объема данных с помощью пользовательского запроса без физических секций, а с целочисленным столбцом для секционирования данных. | **Параметры секции**: Динамический диапазон секций.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец секционирования**: Укажите столбец, используемый для секционирования данных. Можно секционировать по столбцу с целочисленным типом данных.<br>**Верхняя граница раздела** и **Нижняя граница секции**: Укажите, следует ли выполнять фильтрацию по столбцу секционирования для получения данных только между нижним и верхним диапазонами.<br><br>Во время выполнения фабрика данных `?AdfRangePartitionColumnName`заменяет `?AdfRangePartitionUpbound`, и `?AdfRangePartitionLowbound` фактическим именем столбца и диапазонами значений для каждой секции и отправляется в Oracle. <br>Например, если для столбца секции "ID" задана Нижняя граница, равная 1, а верхняя граница — 80, а для параллельного копирования — значение 4, фабрика данных извлекает данные на 4 секции. Их идентификаторы находятся в диапазоне от [1, 20], [21, 40], [41, 60] и [61, 80] соответственно. |

**Пример: запрос с физической секцией**

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

**Пример: запрос с динамическим секционированным диапазоном**

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

При копировании данных из и в Oracle применяются следующие сопоставления. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Oracle | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(поддерживается только в Oracle 10g и более поздних версий) |
| CHAR |Строковое |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (если точность больше 28) |
| INTEGER |Decimal, String (если точность больше 28) |
| LONG |Строковое |
| LONG RAW |Byte[] |
| NCHAR |Строковое |
| NCLOB |Строковое |
| NUMBER |Decimal, String (если точность больше 28) |
| NVARCHAR2 |Строковое |
| RAW |Byte[] |
| ROWID |Строковое |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Строковое |
| TIMESTAMP WITH TIME ZONE |Строковое |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Строковое |
| XML |Строковое |

> [!NOTE]
> Типы данных INTERVAL YEAR TO MONTH и INTERVAL DAY TO SECOND не поддерживаются.


## <a name="next-steps"></a>Следующие шаги
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
