---
title: Копирование данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure | Документация Майкрософт
description: Сведения о перемещении данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure.
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
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048588"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Копирование данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure скопировать данные в Управляемый экземпляр Базы данных SQL Azure и из него. Это продолжение [обзорной статьи о действии копирования](copy-activity-overview.md), в которой представлены общие сведения об этом действии.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Управляемого экземпляра Базы данных SQL Azure можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. И наоборот, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в управляемый экземпляр. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Управляемого экземпляра Базы данных SQL Azure поддерживает:

- Копирование данных с проверкой подлинности SQL.
- (в качестве источника) извлечение данных с использованием SQL-запроса или хранимой процедуры;
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) в SQL Server в настоящее время не поддерживается. 

## <a name="prerequisites"></a>Технические условия

Для доступа к базе данных управляемого экземпляра SQL Azure  **[общедоступную конечную точку](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , можно использовать ADF управляемые среды выполнения интеграции Azure Убедитесь, что вы не только включить общедоступную конечную точку, но также разрешить трафик общедоступной конечной точки в группе безопасности сети, чтобы сделать ADF, возможность подключения к базе данных, выполнив следующие [в этом руководстве](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Для доступа к базе данных управляемого экземпляра SQL Azure **частную конечную точку**настройте [локальная среда выполнения интеграции](create-self-hosted-integration-runtime.md) , можно получить доступ к базе данных. Если вы подготавливаете локальная среда выполнения интеграции в той же виртуальной сети, что и ваш управляемый экземпляр, убедитесь, что на компьютере среды выполнения интеграции находится в другой подсети, чем управляемому экземпляру. Если локальная среда выполнения интеграции подготавливается в другой виртуальной сети, используйте пиринг между виртуальными сетями или подключение "виртуальная сеть — виртуальная сеть". Дополнительные сведения см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей Фабрики данных, характерных для соединителя Управляемого экземпляра Базы данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Управляемого экземпляра Базы данных SQL поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства type необходимо задать значение **SqlServer**. | Да. |
| connectionString |Это свойство указывает сведения о параметре connectionString, которые необходимы для подключения к управляемому экземпляру с использованием проверки подлинности SQL. Дополнительные сведения представлены в примерах ниже. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить пароль в Azure Key Vault, и если это аутентификация SQL, извлеките конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). |Да. |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. (Если ваш управляемый экземпляр имеет общедоступную конечную точку и разрешить ADF для доступа к), можно использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Да. |

**Пример 1. Использование проверки подлинности SQL**.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2. Использование аутентификации SQL с помощью пароля в Azure Key Vault**.

```json
{
    "name": "AzureSqlMILinkedService",
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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Управляемого экземпляра Базы данных SQL.

Чтобы копировать данные из базы данных управляемого экземпляра SQL Azure, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для набора данных должно иметь значение **SqlServerTable**. | Да. |
| tableName |Это свойство содержит имя таблицы или представления в экземпляре базы данных, на которое ссылается связанная служба. | "Нет" для источника. "Да" для приемника. |

**Пример**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Полный список разделов и свойств, доступных для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Управляемого экземпляра Базы данных SQL.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Управляемый экземпляр Базы данных SQL Azure в качестве источника

Чтобы скопировать данные из Управляемого экземпляра Базы данных SQL Azure, задайте тип источника **SqlSource** в действии копирования. В разделе source для действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для источника действия копирования должно иметь значение **SqlSource**. | Да. |
| sqlReaderQuery |Это свойство применяет пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |№ |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |№ |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |№ |

Обратите внимание на следующие моменты.

- Если для параметра **SqlSource** указано значение **sqlReaderQuery**, то действие копирования направляет запрос для получения данных к управляемому экземпляру, указанному в качестве источника. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если свойства **sqlReaderQuery** или **sqlReaderStoredProcedureName** не указаны, то для построения запроса используются столбцы, определенные в разделе structure шаблона JSON для набора данных. Запрос `select column1, column2 from mytable` выполняется для управляемого экземпляра. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример. Использование SQL-запроса**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Управляемый экземпляр Базы данных SQL Azure в качестве приемника

> [!TIP]
> Дополнительные сведения о поддерживаемых записи поведения, конфигурации и рекомендации от [рекомендация по загрузке данных в базе данных управляемого экземпляра SQL Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Чтобы скопировать данные в Управляемый экземпляр Базы данных SQL Azure, задайте тип приемника **SqlSink** в действии копирования. В разделе sink для действия копирования поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство type для приемника действия копирования должно иметь значение **SqlSink**. | Да. |
| writeBatchSize |Количество строк для вставки в таблицу SQL **в пакете**.<br/>Допустимые значения: целое число (количество строк). По умолчанию фабрика данных динамически определяет размер соответствующего пакета, в зависимости от размера строки.  |Нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Допустимые значения: любой промежуток времени. Например, 00:30:00 определяет период 30 минут. |№ |
| preCopyScript |Это свойство определяет для действия копирования SQL-запрос, который выполняется перед записью данных в базу данных управляемого экземпляра. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |№ |
| sqlWriterStoredProcedureName |Это имя хранимой процедуры, которая определяет правила помещения исходных данных в целевую таблицу. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Чтобы создать однократно выполняемую операцию, в которой не используются исходные данные, например для удаления или усечения данных, примените свойство `preCopyScript`. |№ |
| storedProcedureParameters |Эти параметры используются для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |№ |
| sqlWriterTableType |Это свойство определяет имя типа таблицы для использования в хранимой процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |№ |

**Пример 1: Добавление данных**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

Дополнительные сведения см. в разделе [Вызов хранимой процедуры из приемника SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Рекомендации по загрузке данных в базе данных управляемого экземпляра SQL Azure

При копировании данных в базе данных управляемого экземпляра SQL Azure, вам может потребоваться поведение разных записи:

- **[Добавление](#append-data)** : источник данных имеет только новых записей;
- **[Upsert](#upsert-data)** : данные источника с вставок и обновлений;
- **[Перезаписать](#overwrite-entire-table)** : Я хочу перезагрузить таблицы всего измерения каждый раз;
- **[Записи с помощью пользовательской логики](#write-data-with-custom-logic)** : Требуется дополнительная обработка до окончательной вставки в целевую таблицу.

См. соответственно разделы по настройке в ADF и рекомендации.

### <a name="append-data"></a>Добавление данных

Это поведение по умолчанию этого приемника соединителя базы данных управляемого экземпляра SQL Azure и выполните ADF **инструкции bulk insert** на эффективную запись в таблицу. Можно просто настроить источник и приемник соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Параметр я** (желательно, особенно при наличии больших данных для копирования): **большинство эффективный подход** сделать upsert представляет собой следующее: 

- Во-первых, использовать [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) выполнить массовую загрузку всех записей с помощью действия копирования. Как операции для временных таблиц не регистрируются, можно загрузить из миллионов записей в секундах.
- Выполнение действия хранимой процедуры в ADF, чтобы применить [СЛИЯНИЯ](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (или вставки или обновления) инструкции и используйте temp таблицы как источник для выполнения всех обновляет или вставляет в виде одной транзакции, уменьшая объем циклические операции и записи операций. В конце действия хранимой процедуры временной таблицы может быть усечен для обеспечения готовности к следующего цикла upsert. 

В качестве примера в фабрике данных Azure, вы можете создать конвейер с **действие копирования** цепочке с **действия хранимой процедуры** при успешном выполнении. Первый копирует данные из хранилища источника во временную таблицу, скажем « **##UpsertTempTable**"как имя таблицы в наборе данных, затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и очистки временной таблицы.

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

При копировании данных в базе данных управляемого экземпляра SQL Azure, можно также настроить и вызвать хранимую процедуру с дополнительными параметрами, определяемое пользователем.

> [!TIP]
> Вызов хранимой процедуры обрабатывает данные по строкам вместо массовой операции, которая не рекомендуется использовать для копирования большого объема. Дополнительные сведения из [рекомендация по загрузке данных в базе данных управляемого экземпляра SQL Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Можно использовать хранимую процедуру, когда встроенные механизмы копирования не подходят, например Применение дополнительной обработки до окончательной вставки данных источника в целевую таблицу. Некоторые дополнительные примеры обработки: объединение столбцов, просмотр дополнительных значений и вставка в несколько таблиц.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Server. Предположим, что и входные данные, и таблица **Marketing** приемника состоят из трех столбцов: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

**Выходной набор данных:** «tableName» должно быть то же имя параметра типа таблицы в хранимой процедуре (см. ниже скрипт хранимой процедуры).

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

В своей базе данных определите тип таблицы с тем же именем, что и SqlWriterTableType. Для типа таблицы укажите ту же схему, которая возвращается для входных данных.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

В этой хранимой процедуре используются [параметры с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Сопоставление типов данных для Управляемого экземпляра Базы данных SQL

При копировании данных в Управляемый экземпляр Базы данных SQL или из него используются следующие сопоставления между типами данных Управляемого экземпляра Базы данных SQL Azure и промежуточными типами данных Фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет схему и типы данных источника и приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Управляемого экземпляра Базы данных SQL Azure | Промежуточный тип данных Фабрики данных Azure |
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

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure см. в [этой таблице](copy-activity-overview.md##supported-data-stores-and-formats).
