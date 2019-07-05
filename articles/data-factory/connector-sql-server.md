---
title: Копирование данных для SQL Server и обратно с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте о том, как перемещать данные и из базы данных SQL Server, расположенном на локальном или на виртуальной Машине Azure с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443292"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Копирование данных для SQL Server и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию фабрики данных Azure, которую вы используете:"]
> * [Версия 1](v1/data-factory-sqlserver-connector.md)
> * [Текущая версия](connector-sql-server.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из и в базе данных SQL Server. Это продолжение [об обзоре действия копирования](copy-activity-overview.md) статья, в которой приведены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из и в базе данных SQL Server в любой поддерживаемый приемник данных. Или можно скопировать данные из любого поддерживаемого исходного хранилища данных в базу данных SQL Server. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SQL Server поддерживает:

- Версии SQL Server 2016, 2014, 2012, 2008 R2, 2008 и 2005.
- копирование данных с проверкой подлинности SQL или Windows;
- В качестве источника, извлечение данных с помощью SQL-запроса или хранимой процедуры.
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) теперь не поддерживается этим соединителем. Чтобы обойти это, можно использовать [универсальный соединитель ODBC](connector-odbc.md) и драйвер ODBC для SQL Server. Выполните [в этом руководстве](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) с ODBC драйвер загрузки и подключения строки конфигурации.

## <a name="prerequisites"></a>Технические условия

Чтобы скопировать данные из базы данных SQL Server, в нее, необходимо настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md). Среда выполнения интеграции предоставляет встроенный драйвер базы данных SQL Server. Не нужно вручную устанавливать драйвер при копировании данных из или в базу данных SQL Server.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю базы данных SQL Server.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SQL Server поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SqlServer**. | Да |
| connectionString |Укажите **connectionString** сведения, необходимые для подключения к базе данных SQL Server с помощью проверки подлинности SQL или проверку подлинности Windows. Ознакомьтесь с приведенными ниже примерами.<br/>Пометьте это поле как **SecureString** чтобы безопасно хранить его в фабрике данных Azure. Кроме того, пароль можно установить в хранилище ключей Azure. Если проверка подлинности SQL, на включение внесенных изменений `password` настройку из строки подключения. Дополнительные сведения см. в примере JSON, в следующей таблице и [Store учетные данные в хранилище ключей Azure](store-credentials-in-key-vault.md). |Да |
| userName |Укажите имя пользователя, при использовании проверки подлинности Windows. Например, **domainname\\username**. |Нет |
| password |Укажите пароль для учетной записи пользователя, которое вы указали для имени пользователя. Пометьте это поле как **SecureString** чтобы безопасно хранить его в фабрике данных Azure. Вы также можете [ссылка на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Нет |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Если хранилище данных является общедоступным, можно использовать локальную среду выполнения интеграции или среды выполнения интеграции Azure. Если не указан, используется среда выполнения интеграции Azure по умолчанию. |Нет |

>[!TIP]
>Если вы достигнете ошибку с кодом ошибки «UserErrorFailedToConnectToSqlServer», а также сообщение, как «ограничение сеансов для базы данных — XXX и был достигнут», добавить `Pooling=false` строку подключения и повторите попытку.

**Пример 1. Использование проверки подлинности SQL**.

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

**Пример 2. Использовать проверку подлинности SQL с помощью пароля в Azure Key Vault**

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

**Пример 3. Использование проверки подлинности Windows**.

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных SQL Server.

Чтобы скопировать данные из и в базе данных SQL Server, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **SqlServerTable**. | Да |
| tableName |Это свойство является имя таблицы или представления в экземпляре базы данных SQL Server, на которое ссылается связанная служба. | "Нет" для источника, "Да" для приемника |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых SQL Server источника и приемника.

### <a name="sql-server-as-a-source"></a>SQL Server в качестве источника

Чтобы скопировать данные из SQL Server, задайте тип источника **SqlSource** в действии копирования. В разделе source для действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для источника действия копирования должно иметь значение **SqlSource**. | Да |
| sqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |Нет |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать имена и регистром параметров хранимой процедуры. |Нет |

**Примечания:**

- Если **sqlReaderQuery** указывается для **SqlSource**, действие копирования выполняет этот запрос для источника SQL Server для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если вы не укажете либо **sqlReaderQuery** или **sqlReaderStoredProcedureName**, столбцы, определенные в разделе «structure» набора данных JSON используются для создания запроса. Запрос `select column1, column2 from mytable` выполняется в базе данных SQL Server. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример. Используйте SQL-запрос**

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

**Пример. Использование хранимой процедуры**

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
> Дополнительные сведения о поддерживаемых записи поведения, конфигурации и передовом опыте [рекомендация по загрузке данных в SQL Server](#best-practice-for-loading-data-into-sql-server).

Чтобы скопировать данные в базу данных SQL Server, установите тип приемника **SqlSink** в действии копирования. В разделе sink для действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для приемника действия копирования должно иметь значение **SqlSink**. | Да |
| writeBatchSize |Число строк, которые вставляются в таблицу SQL *в пакете*.<br/>Допустимые значения: целое число (количество строк). По умолчанию фабрика данных Azure динамически определяет размер соответствующего пакета, в зависимости от размера строки. |Нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Допустимые значения: для временного диапазона. Например, «00: 30:00» за 30 минут. |Нет |
| preCopyScript |Это свойство задает SQL-запрос для действия копирования для запуска перед записью данных в SQL Server. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет |
| sqlWriterStoredProcedureName |Это имя хранимой процедуры, которая определяет правила помещения исходных данных в целевую таблицу.<br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Чтобы создать однократно выполняемую операцию, в которой не используются исходные данные, например для удаления или усечения данных, примените свойство `preCopyScript`. |Нет |
| storedProcedureParameters |Эти параметры используются для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |
| sqlWriterTableType |Это свойство определяет имя типа таблицы для использования в хранимой процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |Нет |

**Пример 1. Добавление данных**.

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Пример 2. Вызвать хранимую процедуру во время копирования**

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
                "sqlWriterTableType": "CopyTestTableType",
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

При копировании данных в SQL Server, может потребоваться поведение разных записи:

- [Добавление](#append-data): Источник данных имеет только новые записи.
- [Upsert](#upsert-data): Источник данных имеет вставок и обновлений.
- [Перезаписать](#overwrite-the-entire-table): Я хочу перезагрузить таблице всего измерения каждый раз.
- [Записи с помощью пользовательской логики](#write-data-with-custom-logic): Требуется дополнительная обработка до окончательной вставки в целевую таблицу.

См. в соответствующих разделах в фабрике данных Azure и рекомендации по настройке.

### <a name="append-data"></a>Добавление данных

Добавление данных является поведением по умолчанию этого соединителя SQL Server в качестве приемника. Фабрика данных выполняет массовую вставку на эффективную запись в таблицу. Можно настроить источник и приемник соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1.** При наличии большой объем данных для копирования, для выполнения операции upsert использовать следующий подход: 

- Во-первых, используйте [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) для массовой загрузки, все записи с помощью действия копирования. Так как не вошли операций для временных таблиц, можно загрузить из миллионов записей в секундах.
- Выполните действие хранимой процедуры в фабрике данных Azure для применения [СЛИЯНИЯ](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или инструкции INSERT/UPDATE. Используйте временную таблицу, как источник для выполнения всех обновляет или вставляет в виде одной транзакции. Таким образом уменьшается количество циклов и журнал операций. В конце действия хранимой процедуры временная таблица может быть усечен для обеспечения готовности к следующего цикла upsert.

В качестве примера в фабрике данных Azure, вы можете создать конвейер с **действие копирования** цепочке с **действия хранимой процедуры**. Первый копирует данные из хранилища источника во временную таблицу базы данных, например, **##UpsertTempTable**, как имя таблицы в наборе данных. Затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и очистки временной таблицы.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

В вашей базе данных Определите хранимую процедуру с логикой СЛИЯНИЯ, как в следующем примере, который указывает из предыдущего действия хранимой процедуры. Предположим, что цель — **маркетинга** таблицу с тремя столбцами: **ProfileID**, **State** и **Category**. Выполните операцию upsert, на основе **ProfileID** столбца.

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

**Вариант 2.** Вы также можете [вызова хранимой процедуры в рамках действия копирования](#invoke-a-stored-procedure-from-a-sql-sink). Этот подход работает каждая строка в исходной таблице вместо использования инструкции bulk insert как подход по умолчанию, в действие копирования, которое не подходит для крупномасштабного upsert.

### <a name="overwrite-the-entire-table"></a>Перезаписать всю таблицу

Можно настроить **preCopyScript** свойства приемника действия копирования. В этом случае для каждого выполнения действия копирования фабрики данных Azure запускает сценарий сначала. Затем она запускает копирования для вставки данных. Например чтобы перезаписать всю таблицу последними данными, укажите сценарий для удаления всех записей перед массовой загрузки новых данных из источника.

### <a name="write-data-with-custom-logic"></a>Запись данных с помощью пользовательской логики

Инструкции по записи данных с помощью пользовательской логики похожи на описанные в [данных Upsert](#upsert-data) раздел. При необходимости применить дополнительной обработки до окончательной вставки данных источника в целевую таблицу, для большого объема, необходимо выполнить одно из следующих действий: 

- Загрузите во временную таблицу и затем вызвать хранимую процедуру. 
- Вызовите хранимую процедуру во время копирования.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базу данных SQL Server, также можно настроить и вызвать хранимую процедуру с дополнительными параметрами, определяемое пользователем.

> [!TIP]
> Вызов хранимой процедуры обрабатывает данные построчно вместо с использованием массовой операции, которой мы не рекомендуем для крупномасштабного копирования. Дополнительные сведения из [рекомендация по загрузке данных в SQL Server](#best-practice-for-loading-data-into-sql-server).

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Например, если вы хотите применить дополнительной обработки до окончательной вставки данных источника в целевую таблицу. Примеры дополнительной обработки — Если вы хотите объединить столбцы, поиск дополнительных значений и вставки данных в более чем одна таблица.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Server. Предполагается, что входные данные и приемником **маркетинга** каждая таблица имеет три столбца: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

**Выходной набор данных:** «tableName» — это то же имя параметра типа таблицы в хранимой процедуре, как показано в следующем сценарии хранимая процедура:

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Определение **приемника SQL** разделе в действии копирования следующим образом:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

В своей базе данных определите хранимую процедуру с тем же именем, что и **SqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа table в хранимой процедуре является таким же, как **tableName** определены в наборе данных.

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

В базе данных, определите тип таблицы с тем же именем, что **sqlWriterTableType**. Для типа таблицы укажите ту же схему, которая возвращается для входных данных.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

В этой хранимой процедуре используются [параметры с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Сопоставление типов данных для SQL Server

При копировании данных из и в SQL Server, используются следующие сопоставления из типов данных SQL Server для промежуточных типов данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Типы данных SQL Server | Промежуточный тип данных Фабрики данных Azure |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. Если для ваших данных требуется точность больше 28, попробуйте преобразовать их в строковые данные в SQL-запросе.

## <a name="troubleshoot-connection-issues"></a>Устранение неполадок подключения

1. Настройте имеющийся экземпляр SQL Server для удаленных соединений. Запуск **SQL Server Management Studio**, щелкните правой кнопкой мыши **server**и выберите **свойства**. Выберите **подключений** в списке и выберите **разрешить удаленные подключения к этому серверу** "флажок".

    ![Включение удаленных подключений](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Подробные инструкции см. в разделе [настройки параметра конфигурации сервера удаленного доступа](https://msdn.microsoft.com/library/ms191464.aspx).

2. Запуск **диспетчер конфигурации SQL Server**. Разверните узел **Сетевая конфигурация SQL Server** для нужного экземпляра и выберите пункт **Protocols for MSSQLSERVER** (Протоколы для MSSQLSERVER). Протоколы отображаются в области справа. Включите поддержку TCP/IP, щелкнув правой кнопкой мыши **TCP/IP** и выбрав **включить**.

    ![Включение TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Дополнительные сведения и альтернативные способы включения протокола TCP/IP, см. в разделе [Включение или отключение сетевого протокола сервера](https://msdn.microsoft.com/library/ms191294.aspx).

3. В этом же окне, дважды щелкните **TCP/IP** для запуска **свойства TCP/IP** окна.
4. Перейдите на вкладку **IP-адреса** . Прокрутите вниз до **IPAll** раздел. Запишите **TCP-порт**. По умолчанию используется **1433**.
5. Создайте на компьютере **правило брандмауэра Windows** , чтобы разрешить входящий трафик через этот порт. 
6. **Проверьте подключение**. Чтобы подключиться к SQL Server, используя полное доменное имя, используйте SQL Server Management Studio с другого компьютера. Например, `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure см. в [этой таблице](copy-activity-overview.md##supported-data-stores-and-formats).
