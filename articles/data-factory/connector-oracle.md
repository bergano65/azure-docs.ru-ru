---
title: Копирование данных в базу данных Oracle и из нее с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ в базу данных Oracle или из базы данных Oracle в поддерживаемый приемник хранилища данных с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509233"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Копирование данных из Oracle и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которую вы используете:"]
> * [Версия 1](v1/data-factory-onprem-oracle-connector.md)
> * [Текущая версия](connector-oracle.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в базу данных Oracle и из нее. Это продолжение [статьи с обзором действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из базы данных Oracle в любое хранилище данных, поддерживаемое в качестве приемника. Также можно скопировать данные из любого поддерживаемого хранилища данных в базу данных Oracle. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности этот соединитель Oracle поддерживает:

- Следующие версии базы данных Oracle:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)
- Копирование данных с использованием **основные** или **OID** проверок подлинности.
- Параллельное копирование из источника Oracle. См. в разделе [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел с подробными сведениями.

> [!Note]
> Прокси-сервер Oracle не поддерживается.

## <a name="prerequisites"></a>Технические условия

Чтобы копировать данные из базы данных Oracle предварительной версии и в нее, необходимо настроить локальную среду выполнения интеграции. См. дополнительные сведения о среде выполнения интеграции см. в статье [Локальная среда выполнения интеграции](create-self-hosted-integration-runtime.md). Среда выполнения интеграции содержит встроенный драйвер Oracle. Поэтому вам не нужно вручную устанавливать драйвер при копировании данных из базы данных Oracle и в нее.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Oracle.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Oracle поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **Oracle**. | Yes |
| connectionString | Указывает сведения, необходимые для подключения к экземпляру базы данных Oracle. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы можете также поместить пароль в Azure Key Vault и извлечь конфигурацию `password` из строки подключения. Ознакомьтесь с приведенными ниже примерами и подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). <br><br>**Поддерживаемые типы подключений**: вы можете использовать **SID-идентификатор Oracle** или **имя службы Oracle** для идентификации базы данных.<br>— Если вы используете идентификатор безопасности, используйте этот код для подключения: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Если вы используете имя службы, используйте этот код: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

>[!TIP]
>Если при использовании версии Oracle 8i возникает ошибка "ORA-01025: значение параметра UPI вне допустимого диапазона", добавьте `WireProtocolMode=1` в строку подключения и повторите попытку.

**Включить шифрование для подключения Oracle** можно двумя способами:

1.  Чтобы использовать **шифрование 3DES и AES**, на стороне сервера Oracle перейдите к Oracle Advanced Security (OAS) и настройте соответствующие параметры (см. [дополнительные сведения](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)). Соединитель ADF Oracle автоматически согласовывает метод шифрования таким образом, чтобы при установлении соединения с Oracle использовался тот метод шифрования, который вы настроили в OAS.

2.  Чтобы использовать **SSL**, выполните следующие действия:

    1.  Получите сведения о сертификате SSL. Получите сведения о SSL-сертификате в кодировке DER и сохраните выходные данные (----- Begin Certificate ... End Certificate -----) как текстовый файл.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Пример.** Извлеките сведения о сертификате из файла DERcert.cer и сохраните выходные данные в файле cert.txt.

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
    
    2.  Создавайте хранилище ключей или truststore. Следующая команда создает файл truststore с паролем или без него в формате PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Пример:** truststore PKCS12 создан файл с именем MyTrustStoreFile с паролем

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Скопируйте файл truststore на компьютер с запущенной локальной средой выполнения интеграции, например в расположении C:\MyTrustStoreFile.
    4.  Настройте в ADF строку подключения Oracle с использованием `EncryptionMethod=1` и соответствующего значения `TrustStore`/`TrustStorePassword`, например `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Oracle.

Чтобы скопировать данные из базы данных Oracle или в нее, установите для свойства type набора данных значение **OracleTable**. Поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **OracleTable**. | Yes |
| tableName |Имя таблицы в базе данных Oracle, на которое ссылается связанная служба. | Yes |

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Oracle.

### <a name="oracle-as-a-source-type"></a>Oracle в качестве источника данных

> [!TIP]
>
> Дополнительные сведения из [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел о том, как загрузка данных из Oracle, эффективно используя секционирование данных.

Чтобы копировать данные из Oracle, установите тип источника в действии копирования **OracleSource**. В разделе **source** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type источника действия копирования должно иметь значение **OracleSource**. | Yes |
| oracleReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`.<br>Когда вы включаете секционированных нагрузки, необходимо подключить соответствующие встроенные раздел параметров в запросе. См. Примеры в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел. | Нет |
| partitionOptions | Указывает параметры, используемые для загрузки данных из Oracle секционирования данных. <br>Разрешить следующие значения:: **Нет** (по умолчанию), **PhysicalPartitionsOfTable** и **DynamicRange**.<br>При включении режима секционирования (не «None»), также настройте **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** Установка для действия копирования, например 4, который определяет параллельных степени параллельно загружать данные из Oracle База данных. | Нет |
| partitionSettings | Укажите группу параметров для секционирования данных. <br>Применение при параметр секции не `None`. | Нет |
| partitionNames | Список физических секций, которые необходимо скопировать. <br>Применяются при использовании параметра секции `PhysicalPartitionsOfTable`. Если используется запрос для извлечения исходных данных, подключения `?AdfTabularPartitionName` в предложении WHERE. Пример см. в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел. | Нет |
| partitionColumnName | Укажите имя исходного столбца **в целочисленный тип** , будет использоваться секционирование по диапазонам для параллельного копирования. Если не указан, первичный ключ таблицы будет автоматически обнаружен и использоваться в качестве столбца секционирования. <br>Применяются при использовании параметра секции `DynamicRange`. Если используется запрос для извлечения исходных данных, подключения `?AdfRangePartitionColumnName` в предложении WHERE. Пример см. в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел. | Нет |
| partitionUpperBound | Максимальное значение столбца секционирования, чтобы скопировать данные. <br>Применяются при использовании параметра секции `DynamicRange`. Если используется запрос для извлечения исходных данных, подключения `?AdfRangePartitionUpbound` в предложении WHERE. Пример см. в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел. | Нет |
| partitionLowerBound | Минимальное значение столбца секционирования, чтобы скопировать данные. <br>Применяются при использовании параметра секции `DynamicRange`. Если используется запрос для извлечения исходных данных, подключения `?AdfRangePartitionLowbound` в предложении WHERE. Пример см. в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел. | Нет |

**Пример: копирование данных с помощью простого запроса без секционирования**

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

Дополнительные примеры приведены в [параллельное копирование из Oracle](#parallel-copy-from-oracle) раздел.

### <a name="oracle-as-a-sink-type"></a>Oracle в качестве приемника

Чтобы скопировать данные в базу данных Oracle, в операции копирования задайте тип приемника **OracleSink**. В разделе **sink** действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type приемника действия копирования должно иметь значение **OracleSink**. | Yes |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize.<br/>Допустимые значения: целое число (количество строк). |Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: временной диапазон. Например, 00:30:00 (30 минут). | Нет |
| preCopyScript | Перед записью данных в базу данных Oracle при каждом запуске указывайте SQL-запрос для выполнения операции копирования. Это свойство можно использовать для очистки предварительно загруженных данных. | Нет |

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

## <a name="parallel-copy-from-oracle"></a>Параллельное копирование из Oracle

Соединитель Oracle фабрики данных предоставляет встроенные данные, секционирование для копирования данных из Oracle в параллельном режиме с высокой производительностью. Можно найти параметры секционирования данных в действие копирования "->" источник Oracle:

![Параметры секции](./media/connector-oracle/connector-oracle-partition-options.png)

При включении секционированных копирования, фабрика данных запускает параллельные запросы к источнику Oracle для загрузки данных по разделам. Настраивается и контролируется с помощью параллельных степени **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** задание для действия копирования. Например, если задать `parallelCopies` как четыре, одновременно создаваемые фабрикой данных, и четырех запросов выполняется на основе указанного раздела и параметры, иных извлечение данных из базы данных Oracle.

Рекомендуется включить параллельное копирование с использованием данных, секционирование, особенно в том случае, при загрузке большого объема данных из базы данных Oracle. Ниже приведены рекомендуемые конфигурации для различных сценариев.

| Сценарий                                                     | Рекомендуемые параметры                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Полная загрузка из большой таблицы с физическими секциями          | **Секции параметра**: Физических секций таблицы. <br><br/>Во время выполнения фабрика данных автоматически обнаруживать физические секции и скопируйте данные по разделам. |
| Полная загрузка из большой таблицы без физических секций, тогда как для целочисленного столбца для секционирования данных | **Параметров секции**: Раздел динамического диапазона.<br>**Столбец секционирования**: Укажите столбец, используемый для секционирования данных. Если не будет использован указанный столбец первичного ключа. |
| Загрузить большой объем данных с помощью пользовательского запроса под с физическими секциями | **Секции параметра**: Физических секций таблицы.<br>**Запрос**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Имя секции**: Укажите имена для копирования данных из раздела. Если не указан, ADF автоматически обнаруживает физические секции для таблицы, указанной в набор данных Oracle.<br><br>Во время выполнения, замените фабрики данных `?AdfTabularPartitionName` с именем фактический раздел и отправить в Oracle. |
| Загрузить большой объем данных, с помощью пользовательского запроса под без физических секций и с целочисленным столбцом для секционирования данных | **Параметров секции**: Раздел динамического диапазона.<br>**Запрос**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Столбец секционирования**: Укажите столбец, используемый для секционирования данных. Можно разделить по отношению к столбцу с типом данных integer.<br>**Верхняя граница секции** и **нижняя граница секции**: Укажите, если нужно выполнить фильтрацию по отношению к столбцу секционирования для извлечения только данные между нижним и верхним диапазоном.<br><br>Во время выполнения, замените фабрики данных `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, и `?AdfRangePartitionLowbound` с действительное имя столбца и значением диапазоны для каждой секции и отправку Oracle. <br>Например, если значение в столбце секционирования «ID» с нижней границей, 1, а верхняя граница как 80, с набором параллельное копирование 4, ADF получать данные по 4 разделов с Идентификатором между [1,20], [21, 40], [41, 60] и [61, 80]. |

**Пример: запрос с физической секции**

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

**Пример: запрос с секцией динамического диапазона**

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

При копировании данных из базы данных Oracle и в нее для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных Oracle. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Oracle | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(поддерживается только в Oracle 10g и более поздних версий) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (если точность больше 28) |
| INTEGER |Decimal, String (если точность больше 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (если точность больше 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Типы данных INTERVAL YEAR TO MONTH и INTERVAL DAY TO SECOND не поддерживаются.


## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных.
