---
title: Копирование данных в SQL Server и из них с помощью фабрики данных Azure
description: Узнайте, как перемещать данные в SQL Server базу данных, которая находится локально или на виртуальной машине Azure, с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 424007c6bd34c0d582af8cd4df00ce7f5fc7fb0f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680147"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Копирование данных в SQL Server и из них с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию фабрики данных Azure, которую вы используете:"]
> * [Версия 1](v1/data-factory-sqlserver-connector.md)
> * [Текущая версия](connector-sql-server.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в SQL Server базу данных и в нее. Она основана на статье [Обзор действия копирования](copy-activity-overview.md) , в которой представлен общий обзор действия копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель SQL Server поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Данные из базы данных SQL Server можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Или можно скопировать данные из любого поддерживаемого исходного хранилища данных в базу данных SQL Server. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SQL Server поддерживает:

- SQL Server версии 2016, 2014, 2012, 2008 R2, 2008 и 2005.
- копирование данных с проверкой подлинности SQL или Windows;
- В качестве источника, получая данные с помощью SQL-запроса или хранимой процедуры.
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) не поддерживается.

>[!NOTE]
>SQL Server [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) сейчас не поддерживается этим соединителем. Для решения этой проблемы можно использовать [универсальный соединитель ODBC](connector-odbc.md) и драйвер SQL Server ODBC. Следуйте указаниям [по](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) загрузке драйвера ODBC и настройке строки подключения.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к SQL Server соединителю базы данных.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SQL Server поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SqlServer**. | Да |
| connectionString |Укажите сведения о **ConnectionString** , необходимые для подключения к SQL Server базе данных с помощью проверки подлинности SQL или Windows. Ознакомьтесь с приведенными ниже примерами.<br/>Пометьте это поле как **SecureString** , чтобы безопасно хранить его в фабрике данных Azure. Вы также можете добавить пароль в Azure Key Vault. Если это проверка подлинности SQL, вытяните конфигурацию `password` из строки подключения. Дополнительные сведения см. в примере JSON, который следует за таблицей, и [Храните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| userName |При использовании проверки подлинности Windows укажите имя пользователя. Например, **domainname\\username**. |Нет |
| пароль |Укажите пароль для учетной записи пользователя, указанной для имени пользователя. Пометьте это поле как **SecureString** , чтобы безопасно хранить его в фабрике данных Azure. Или можно [сослаться на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Нет |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Дополнительные сведения см. в разделе " [Предварительные требования](#prerequisites) ". Если значение не указано, используется среда выполнения интеграции Azure по умолчанию. |Нет |

>[!TIP]
>При возникновении ошибки с кодом ошибки "UserErrorFailedToConnectToSqlServer" и сообщением, например "ограничение сеанса для базы данных равно XXX и достигнуто", добавьте `Pooling=false` в строку подключения и повторите попытку.

**Пример 1. Использование проверки подлинности SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Использование проверки подлинности SQL с паролем в Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

**Пример 3. Использование проверки подлинности Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных SQL Server.

Чтобы скопировать данные из SQL Server базу данных и в нее, поддерживаются следующие свойства:

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **SqlServerTable**. | Да |
| schema | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| таблица | Имя таблицы или представления. |"Нет" для источника, "Да" для приемника  |
| tableName | Имя таблицы или представления со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | "Нет" для источника, "Да" для приемника |

**Пример**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server в качестве источника

Чтобы скопировать данные из SQL Server, задайте тип источника **SqlSource** в действии копирования. В разделе source для действия копирования поддерживаются следующие свойства.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для источника действия копирования должно иметь значение **SqlSource**. | Да |
| sqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |Нет |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |

**Примечания:**

- Если для **SqlSource**указан **sqlReaderQuery** , действие копирования выполняет этот запрос к источнику SQL Server для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если не указать ни **sqlReaderQuery** , ни **sqlReaderStoredProcedureName**, то для создания запроса используются столбцы, определенные в разделе Structure набора данных JSON. Запрос `select column1, column2 from mytable` выполняется по отношению к SQL Server. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример. Использование SQL Query**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Пример. использование хранимой процедуры**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Определение хранимой процедуры**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server в качестве приемника

> [!TIP]
> Дополнительные сведения о поддерживаемых поведениях записи, конфигурациях и рекомендациях см. в статье рекомендации [по загрузке данных в SQL Server](#best-practice-for-loading-data-into-sql-server).

Чтобы скопировать данные в базу данных SQL Server, установите тип приемника **SqlSink** в действии копирования. В разделе sink для действия копирования поддерживаются следующие свойства.

| Свойство | Description (Описание) | Обязательно |
|:--- |:--- |:--- |
| type | Свойство type для приемника действия копирования должно иметь значение **SqlSink**. | Да |
| writeBatchSize |Число строк, вставляемых в таблицу SQL для *каждого пакета*.<br/>Допустимые значения: целое число (количество строк). По умолчанию фабрика данных Azure динамически определяет соответствующий размер пакета в зависимости от размера строки. |Нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Допустимые значения — для интервала времени. Например, "00:30:00" в течение 30 минут. Если значение не указано, время ожидания по умолчанию равно "02:00:00". |Нет |
| preCopyScript |Это свойство задает SQL-запрос для выполнения действия копирования перед записью данных в SQL Server. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, выполняемых только один раз и не имеющих никаких действий с исходными данными, например Delete или TRUNCATE, используйте свойство `preCopyScript`. | Нет |
| storedProcedureTableTypeParameterName |Имя параметра табличного типа, указанного в хранимой процедуре.  |Нет |
| sqlWriterTableType |Имя типа таблицы, используемое в хранимой процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| таблеоптион | Указывает, следует ли автоматически создавать таблицу приемника, если она не существует на основе исходной схемы. Автоматическое создание таблицы не поддерживается, если приемник указывает, что хранимая процедура или промежуточное копирование настроены в действии копирования. Допустимые значения: `none` (по умолчанию), `autoCreate`. |Нет |

**Пример 1. Добавление данных**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Пример 2. вызов хранимой процедуры во время копирования**

Дополнительные сведения см. в разделе [Вызов хранимой процедуры из приемника SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Рекомендации по загрузке данных в SQL Server

При копировании данных в SQL Server может потребоваться другое поведение при записи.

- [Append](#append-data): мои исходные данные имеют только новые записи.
- [Upsert](#upsert-data): исходные данные имеют как вставки, так и обновления.
- [Перезаписать](#overwrite-the-entire-table): я хочу каждый раз загружать всю таблицу измерения.
- [Запись с помощью пользовательской логики](#write-data-with-custom-logic): требуется дополнительные операции перед окончательным вставкой в целевую таблицу.

Сведения о настройке в фабрике данных Azure и рекомендациях см. в соответствующих разделах.

### <a name="append-data"></a>Добавить данные

Добавление данных — это поведение по умолчанию для соединителя приемника SQL Server. Фабрика данных Azure обеспечивает эффективную запись в таблицу с помощью операций групповой вставки. Источник и приемник можно настроить соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1.** При наличии большого объема данных для копирования используйте следующий подход для выполнения Upsert: 

- Во-первых, используйте [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) для выполнения групповой загрузки всех записей с помощью действия копирования. Поскольку операции с временными таблицами не регистрируются, можно загружать миллионы записей за считаные секунды.
- Выполните действие хранимой процедуры в фабрике данных Azure, чтобы применить инструкцию [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или INSERT или Update. Используйте временную таблицу в качестве источника для выполнения всех операций обновления или вставки как одной транзакции. Таким образом уменьшается количество циклов обработки и операций с журналом. В конце действия хранимой процедуры можно усечь временную таблицу, чтобы она была готова к следующему циклу Upsert.

Например, в фабрике данных Azure можно создать конвейер с **действием копирования** , связанным с **действием хранимой процедуры**. Первый копирует данные из исходного хранилища во временную таблицу базы данных, например **# #UpsertTempTable**, как имя таблицы в наборе данных. Затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и очистки временной таблицы.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

В базе данных Определите хранимую процедуру с помощью логики слияния, как показано в следующем примере, на который указывает действие из предыдущего действия хранимой процедуры. Предположим, что целевым объектом является **маркетинговая** таблица с тремя столбцами: **ProfileID**, **State**и **Category**. Выполните Upsert на основе столбца **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Вариант 2.** Можно также выбрать [вызов хранимой процедуры в рамках действия копирования](#invoke-a-stored-procedure-from-a-sql-sink). Этот подход выполняет каждую строку в исходной таблице вместо использования инструкции массовой вставки в качестве подхода по умолчанию в действии копирования, которое не подходит для крупномасштабных Upsert.

### <a name="overwrite-the-entire-table"></a>Перезаписать всю таблицу

Свойство **preCopyScript** можно настроить в приемнике действия копирования. В этом случае для каждого выполняемого действия копирования фабрика данных Azure сначала запускает сценарий. Затем выполняется копирование для вставки данных. Например, чтобы перезаписать всю таблицу последними данными, укажите скрипт, чтобы сначала удалить все записи, прежде чем выполнять пакетную загрузку новых данных из источника.

### <a name="write-data-with-custom-logic"></a>Запись данных с помощью пользовательской логики

Действия по записи данных с помощью пользовательской логики похожи на процедуры, описанные в разделе [данных Upsert](#upsert-data) . Если необходимо применить дополнительную обработку перед окончательной вставкой исходных данных в целевую таблицу, можно выполнить одно из двух действий. 

- Загрузить во временную таблицу, а затем вызвать хранимую процедуру. 
- Вызов хранимой процедуры во время копирования.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в SQL Server базу данных можно также настроить и вызвать пользовательскую хранимую процедуру с дополнительными параметрами. В этой хранимой процедуре используются [параметры с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> При вызове хранимой процедуры строка данных обрабатывается, а не с помощью массовой операции, которая не рекомендуется для крупномасштабной копии. Узнайте больше о том, как [загружать данные в SQL Server](#best-practice-for-loading-data-into-sql-server).

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Примером может быть применение дополнительной обработки перед окончательной вставкой исходных данных в целевую таблицу. Некоторые дополнительные примеры обработки используются для объединения столбцов, поиска дополнительных значений и вставки данных в более чем одну таблицу.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Server. Предположим, что у входных данных и **маркетинговой** таблицы приемника есть три столбца: **ProfileID**, **State**и **Category**. Выполните Upsert на основе столбца **ProfileID** и примените его только к определенной категории с названием «Product а».

1. В базе данных определите тип таблицы с тем же именем, что и **и sqlwritertabletype**. Для типа таблицы укажите ту же схему, которая возвращается для входных данных.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. В своей базе данных определите хранимую процедуру с тем же именем, что и **SqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа таблицы в хранимой процедуре совпадает с именем **TableName** , определенным в наборе данных.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. В фабрике данных Azure Определите раздел **SQL Sink** в действии копирования следующим образом:

    ```json
    "sink": {
        "type": "SqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Сопоставление типов данных для SQL Server

При копировании данных из и в SQL Server следующие сопоставления используются из SQL Server типов данных в промежуточные типы данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Типы данных SQL Server | Промежуточный тип данных Фабрики данных Azure |
|:--- |:--- |
| bigint |Int64 |
| binary; |Byte[] |
| bit |Логический |
| char; |String, Char[] |
| дата |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| DECIMAL |DECIMAL |
| Атрибут FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| изображение |Byte[] |
| int |Int32 |
| money |DECIMAL |
| nchar; |String, Char[] |
| ntext |String, Char[] |
| numeric |DECIMAL |
| nvarchar |String, Char[] |
| real; |Single |
| rowversion |Byte[] |
| smalldatetime; |DateTime |
| smallint |Int16 |
| smallmoney |DECIMAL |
| sql_variant |Объект |
| text |String, Char[] |
| time |TimeSpan |
| Timestamp |Byte[] |
| tinyint; |Int16 |
| uniqueidentifier |Guid |
| varbinary; |Byte[] |
| varchar. |String, Char[] |
| xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. Если для ваших данных требуется точность больше 28, попробуйте преобразовать их в строковые данные в SQL-запросе.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="troubleshoot-connection-issues"></a>Устранение неполадок с подключением

1. Настройте экземпляр SQL Server для приема удаленных подключений. Запустите **SQL Server Management Studio**, щелкните правой кнопкой мыши **сервер**и выберите пункт **Свойства**. В списке выберите **подключения** и установите флажок **Разрешить удаленные соединения с этим сервером** .

    ![Включение удаленных подключений](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Подробные инструкции см. [в разделе Настройка параметра конфигурации сервера Remote Access](https://msdn.microsoft.com/library/ms191464.aspx).

2. Запустите **Диспетчер конфигурации SQL Server**. Разверните узел **Сетевая конфигурация SQL Server** для нужного экземпляра и выберите пункт **Protocols for MSSQLSERVER** (Протоколы для MSSQLSERVER). Протоколы отображаются на правой панели. Включите TCP/IP, щелкнув правой кнопкой мыши **TCP/IP** и выбрав **включить**.

    ![Включение TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Дополнительные сведения и альтернативные способы включения протокола TCP/IP см. в разделе [Включение или отключение сетевого протокола сервера](https://msdn.microsoft.com/library/ms191294.aspx).

3. В том же окне дважды щелкните **TCP/IP** , чтобы открыть окно **свойств TCP/IP** .
4. Перейдите на вкладку **IP-адреса** . Прокрутите вниз, чтобы увидеть раздел **IPAll** . Запишите **TCP-порт**. Значение по умолчанию — **1433**.
5. Создайте на компьютере **правило брандмауэра Windows** , чтобы разрешить входящий трафик через этот порт. 
6. **Проверить подключение**. чтобы подключиться к SQL Server с помощью полного имени, используйте SQL Server Management Studio с другого компьютера. Например, `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure см. в [этой таблице](copy-activity-overview.md##supported-data-stores-and-formats).
