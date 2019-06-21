---
title: Копирование данных в базу данных SQL Server и из нее с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как с помощью фабрики данных Azure перемещать данные в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure.
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
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275980"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Копирование данных в базу данных SQL Server и из нее с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которую вы используете:"]
> * [Версия 1](v1/data-factory-sqlserver-connector.md)
> * [Текущая версия](connector-sql-server.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в базу данных SQL Server и из нее. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Вы можете скопировать данные из любого поддерживаемого в качестве источника хранилища данных в базу данных SQL Server или из нее в любое поддерживаемое в качестве приемника хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель SQL Server поддерживает:

- SQL Server версии 2016, 2014, 2012, 2008 R2, 2008 и 2005;
- копирование данных с помощью проверки подлинности **SQL** или **Windows**;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- добавление данных в целевую таблицу или вызов хранимой процедуры во время копирования с помощью пользовательской логики (в качестве приемника).

>[!NOTE]
>SQL Server **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** теперь не поддерживается этим соединителем. Чтобы обойти это, можно использовать [универсальный соединитель ODBC](connector-odbc.md) и драйвер ODBC для SQL Server. Выполните [в этом руководстве](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) с ODBC драйвер загрузки и подключения строки конфигурации.

## <a name="prerequisites"></a>Технические условия

Чтобы скопировать данные из базы данных SQL Server, которая не является общедоступной, необходимо настроить локальную среду выполнения интеграции. Дополнительные сведения см. в статье [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md). Среда выполнения интеграции предоставляет встроенный драйвер SQL Server, поэтому при копировании данных из SQL Server и обратно вам не потребуется устанавливать драйвер вручную.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения объектов фабрики данных, характерных для соединителя базы данных SQL Server.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы SQL Server поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type необходимо задать значение **SqlServer** | Yes |
| connectionString |Укажите сведения о параметре connectionString, необходимые для подключения к базе данных SQL Server с помощью проверки подлинности SQL или Windows. Ознакомьтесь с приведенными ниже примерами.<br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить пароль в Azure Key Vault, и если это аутентификация SQL, извлеките конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| userName |При использовании проверки подлинности Windows укажите имя пользователя. Например, **domainname\\username**. |Нет |
| password |Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Нет |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет |

>[!TIP]
>Если вы получили ошибку с кодом ошибки UserErrorFailedToConnectToSqlServer и сообщение типа "Предел сеанса для базы данных — XXX, и он был достигнут", добавьте `Pooling=false` в строку подключения и повторите попытку.

**Пример 1. Использование проверки подлинности SQL**

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

**Пример 2. Использование аутентификации SQL с паролем в Azure Key Vault**.

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

**Пример 3. Использование проверки подлинности Windows**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных SQL Server.

Чтобы скопировать данные в базе данных SQL Server, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type набора данных необходимо задать следующее значение: **SqlServerTable**. | Yes |
| tableName |Имя таблицы или представления экземпляра базы данных SQL Server, на который ссылается связанная служба. | "Нет" для источника, "Да" для приемника |

**Пример.**

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых SQL Server в качестве источника и приемника.

### <a name="sql-server-as-source"></a>SQL Server в качестве источника

Чтобы скопировать данные из SQL Server, задайте тип источника **SqlSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойству type источника действия копирования необходимо задать значение **SqlSource**. | Yes |
| sqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. |Нет |
| sqlReaderStoredProcedureName |Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |

**Примечания:**

- Если для SqlSource указано **sqlReaderQuery**, то действие копирования выполняет этот запрос с целью получения данных у источника базы данных SQL Server. Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
- Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для создания запроса (`select column1, column2 from mytable`) к базе данных SQL Server будут использованы столбцы, определенные в разделе structure набора данных JSON. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример. Использование SQL-запроса**

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

**Определение хранимой процедуры:**

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

### <a name="sql-server-as-sink"></a>SQL Server в качестве приемника

> [!TIP]
> Дополнительные сведения о поддерживаемых записи поведения, конфигурации и рекомендации от [рекомендация по загрузке данных в SQL Server](#best-practice-for-loading-data-into-sql-server).

Чтобы скопировать данные в базу данных SQL Server, установите тип приемника **SqlSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type приемника действия копирования должно иметь следующее значение: **SqlSink**. | Yes |
| writeBatchSize |Количество строк для вставки в таблицу SQL **в пакете**.<br/>Допустимые значения: целое число (количество строк). По умолчанию фабрика данных динамически определяет размер соответствующего пакета, в зависимости от размера строки. |Нет |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: промежуток времени. Пример: "00:30:00" (30 минут). |Нет |
| preCopyScript |Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в базу данных SQL Server. Он будет однократно вызываться при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет |
| sqlWriterStoredProcedureName |Имя хранимой процедуры, которая определяет, как применить исходные данные в целевую таблицу.<br/>Обратите внимание, что эта хранимая процедура будет **вызываться для каждого пакета**. Чтобы однократно выполнить операцию, в которой не используются исходные данные, например удаление или усечение, примените свойство `preCopyScript`. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |
| sqlWriterTableType |Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. |Нет |

**Пример 1: Добавление данных**

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

**Пример 2: вызова хранимой процедуры во время копирования**

Дополнительные сведения см. в разделе [Вызов хранимой процедуры для приемника SQL](#invoking-stored-procedure-for-sql-sink).

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

При копировании данных в SQL Server, вам может потребоваться поведение разных записи:

- **[Добавление](#append-data)** : источник данных имеет только новых записей;
- **[Upsert](#upsert-data)** : данные источника с вставок и обновлений;
- **[Перезаписать](#overwrite-entire-table)** : Я хочу перезагрузить таблицы всего измерения каждый раз;
- **[Записи с помощью пользовательской логики](#write-data-with-custom-logic)** : Требуется дополнительная обработка до окончательной вставки в целевую таблицу.

См. соответственно разделы по настройке в ADF и рекомендации.

### <a name="append-data"></a>Добавление данных

Это поведение по умолчанию этого соединителя SQL Server в качестве приемника, и выполните ADF **инструкции bulk insert** на эффективную запись в таблицу. Можно просто настроить источник и приемник соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Параметр я** (желательно, особенно при наличии больших данных для копирования): **большинство эффективный подход** сделать upsert представляет собой следующее: 

- Во-первых, использовать [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) выполнить массовую загрузку всех записей с помощью действия копирования. Как операции для временных таблиц не регистрируются, можно загрузить из миллионов записей в секундах.
- Выполнение действия хранимой процедуры в ADF, чтобы применить [СЛИЯНИЯ](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (или вставки или обновления) инструкции и используйте temp таблицы как источник для выполнения всех обновляет или вставляет в виде одной транзакции, уменьшая объем циклические операции и записи операций. В конце действия хранимой процедуры временной таблицы может быть усечен для обеспечения готовности к следующего цикла upsert. 

В качестве примера в фабрике данных Azure, вы можете создать конвейер с **действие копирования** цепочке с **действия хранимой процедуры** при успешном выполнении. Первый копирует данные из исходного хранилища в базе данных временную таблицу, предположим, что " **##UpsertTempTable**" как имя таблицы в наборе данных, затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и удалите временную таблицу.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

В базе данных определения хранимой процедуры с ОБЪЕДИНЕННУЮ логику, как показано ниже, который указывает из выше действия хранимой процедуры. При условии, что целевой **маркетинга** таблицу с тремя столбцами: **ProfileID**, **состояние**, и **категории**, и выполните операцию upsert, на основе **ProfileID** столбца.

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

**Параметр II:** в качестве альтернативы Вы можете [вызова хранимой процедуры в рамках действия копирования](#invoking-stored-procedure-for-sql-sink), во время примечание, этот подход выполняется для каждой строки в исходной таблице, а не использование bulk insert как подход, по умолчанию в действии копирования таким образом этот текст не подходит для крупномасштабного upsert.

### <a name="overwrite-entire-table"></a>Перезаписать всю таблицу

Можно настроить **preCopyScript** приемника свойство в действии копирования, в этом случае для каждого выполнения действия копирования фабрики данных AZURE выполняется скрипт во-первых, затем выполнения действия копирования для вставки данных. Например, чтобы перезаписать всю таблицу последними данными, перед массовой загрузкой новых данных из источника можно указать сценарий для удаления всех записей.

### <a name="write-data-with-custom-logic"></a>Запись данных с помощью пользовательской логики

Примерно так, как описано в разделе [Upsert данных](#upsert-data) раздел, если вам нужно применить дополнительной обработки до окончательной вставки данных источника в целевую таблицу, вы можете) для больших объемов загрузки во временную таблицу, затем вызвать сохраненный процедура, или б) вызов хранимой процедуры во время копирования.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базе данных SQL Server, можно также настроить и вызвать хранимую процедуру с дополнительными параметрами, определяемое пользователем.

> [!TIP]
> Вызов хранимой процедуры обрабатывает данные по строкам вместо массовой операции, которая не рекомендуется использовать для копирования большого объема. Дополнительные сведения из [рекомендация по загрузке данных в SQL Server](#best-practice-for-loading-data-into-sql-server).

Можно использовать хранимую процедуру, когда встроенные механизмы копирования не подходят, например Применение дополнительной обработки до окончательной вставки данных источника в целевую таблицу. Некоторые дополнительные примеры обработки: объединение столбцов, просмотр дополнительных значений и вставка в несколько таблиц.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Server. Предположим, что и входные данные, и таблица **Marketing** приемника состоят из трех столбцов: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

**Выходной набор данных:** «tableName» должно быть то же имя параметра типа таблицы в хранимой процедуре (см. ниже скрипт хранимой процедуры).

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

Определение **приемника SQL** разделе в действии копирования следующим образом.

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

В своей базе данных определите хранимую процедуру с тем же именем, что и **SqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа таблицы в хранимой процедуре должно совпадать с именем **tableName**, заданным в наборе данных.

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

В своей базе данных определите тип таблицы с тем же именем, что и SqlWriterTableType. Обратите внимание, что схема типа таблицы должны быть той же, что и схема, возвращаемая входными данными.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Сопоставление типов SQL Server

При копировании данных из/в SQL Server используются следующие сопоставления типов данных SQL Server с промежуточными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Типы данных SQL Server | Тип промежуточных данных фабрики данных |
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
| Xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal (десятичное число), Фабрика данных Azure поддерживает точность до 28. При наличии данных с точностью больше 28 их можно преобразовать в строку в SQL-запросе.

## <a name="troubleshooting-connection-issues"></a>Устранение неполадок с подключением

1. Настройте SQL Server для приема удаленных подключений. Запустите **SQL Server Management Studio**, щелкните правой кнопкой мыши свой **сервер** и выберите пункт **Свойства**. Выберите в списке пункт **Подключения** и установите флажок **Allow remote connections to the server** (Разрешить удаленные подключения к этому серверу).

    ![Включение удаленных подключений](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Подробные инструкции см. в статье [Настройка параметра конфигурации сервера remote access](https://msdn.microsoft.com/library/ms191464.aspx).

2. Запустите **диспетчер конфигурации SQL Server**. Разверните узел **Сетевая конфигурация SQL Server** для нужного экземпляра и выберите пункт **Protocols for MSSQLSERVER** (Протоколы для MSSQLSERVER). Вы должны увидеть протоколы на панели справа. Включите TCP/TP, щелкнув правой кнопкой мыши имя протокола **TCP/IP** и выбрав пункт **Включить**.

    ![Включение TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Подробные сведения и альтернативные способы включения протокола TCP/IP см. в статье [Включение или отключение сетевого протокола сервера](https://msdn.microsoft.com/library/ms191294.aspx).

3. В этом же окне дважды щелкните **TCP/IP**, чтобы открыть окно **TCP/IP Properties** (Свойства TCP/IP).
4. Перейдите на вкладку **IP-адреса** . Прокрутите вниз до раздела **IPAll** . Запишите значение параметра **Порт TCP** (**1433** по умолчанию).
5. Создайте на компьютере **правило брандмауэра Windows** , чтобы разрешить входящий трафик через этот порт.  
6. **Проверьте подключение**. Чтобы подключиться к SQL Server, используя полное имя, используйте SQL Server Management Studio с другого компьютера. Например, `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
