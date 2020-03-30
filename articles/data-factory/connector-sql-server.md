---
title: Копирование данных на сервер и с сервера S'L
description: Узнайте о том, как перемещать данные в базу данных и из базы данных S'L Server, которая находится в помещении или в VM Azure, с помощью Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: e7f91d60a0e181690859da64e491c4c0fa1e1a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238718"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Копирование данных на сервер и из сервера S'L с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию фабрики данных Azure:"]
> * [Версия 1](v1/data-factory-sqlserver-connector.md)
> * [Текущая версия](connector-sql-server.md)

В этой статье излагается, как использовать активность копирования на фабрике данных Azure для копирования данных из базы данных сервера S'L Server. Он основывается на статье [обзора активности копирования,](copy-activity-overview.md) в ней представлен общий обзор действия копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем сервера S'L поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Вы можете скопировать данные из базы данных сервера S'L в любой поддерживаемый хранилище данных раковины. Кроме того, можно скопировать данные из любого поддерживаемого хранилища исходных данных в базу данных сервера S'L Server. Список хранилив данных, которые поддерживаются в качестве источников или появляются в потоке активности копирования, см. [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats)

В частности, этот соединитель SQL Server поддерживает:

- Версии сервера 2016, 2014, 2012, 2008 R2, 2008 и 2005.
- копирование данных с проверкой подлинности SQL или Windows;
- В качестве источника, получение данных с помощью запроса S'L или сохраненной процедуры.
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

[S'L Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) не поддерживается.

>[!NOTE]
>Сервер S'L [Всегда шифруется](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) не поддерживается этим разъемом сейчас. Для работы можно использовать [универсальный разъем ODBC](connector-odbc.md) и драйвер ODBC сервера S'L. Следуйте [этому руководству](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) с загрузкой драйвера ODBC и конфигурациями строки соединения.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Data Factory, специфичных для разъема базы данных S'L Server.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы, связанной с сервером S'L:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на **SqlServer.** | Да |
| connectionString |Укажите информацию **о подключении к** базе данных сервера S'L, используя либо проверку подлинности S'L, либо проверку подлинности Windows. Ознакомьтесь с приведенными ниже примерами.<br/>Вы также можете поместить пароль в Хранилище ключей Azure. Если это проверка подлинности S'L, вытащите конфигурацию `password` из строки соединения. Для получения дополнительной информации смотрите пример JSON, следуя за таблицей и [учетными данными хранилища в Azure Key Vault.](store-credentials-in-key-vault.md) |Да |
| userName |Укажите имя пользователя, если вы используете проверку подлинности Windows. Например, **domainname\\username**. |нет |
| password |Укажите пароль для учетной записи пользователя, указанный для имени пользователя. Отметьте это поле как **SecureString** для безопасного хранения на фабрике данных Azure. Или вы можете [ссылаться на секрет, хранящийся в Хранилище ключей Azure.](store-credentials-in-key-vault.md) |нет |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Узнайте больше из [раздела Prerequisites.](#prerequisites) Если не указано, используется время выполнения интеграции Azure по умолчанию. |нет |

>[!TIP]
>Если вы нажмете на ошибку с кодом ошибки "UserErrorFailedToConnectToSqLServer" и сообщением типа `Pooling=false` "Лимит сеанса для базы данных XXX и был достигнут", добавьте строку подключения и повторите попытку.

**Пример 1: Используйте проверку подлинности СЗЛ**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2: Используйте проверку подлинности СЗЛ с паролем в Хранилище ключей Azure**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**Пример 3: Использование аутентификации Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
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

Полный список разделов и свойств, доступных [datasets](concepts-datasets-linked-services.md) для определения наборов данных, см. В этом разделе приводится список свойств, поддерживаемых набором данных сервера S'L.

Для копирования данных из базы данных сервера S'L Server поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на **sqlServerTable.** | Да |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Название таблицы/просмотра. |"Нет" для источника, "Да" для приемника  |
| tableName | Название таблицы/вид со схемой. Это свойство поддерживается для обратной совместимости. Для новой рабочей `schema` `table`нагрузки, использование и . | "Нет" для источника, "Да" для приемника |

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

Полный список разделов и свойств, доступных для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником и раковиной сервера S'L Server.

### <a name="sql-server-as-a-source"></a>Сервер СЗЛ как источник

Чтобы скопировать данные из SQL Server, задайте тип источника **SqlSource** в действии копирования. В разделе source для действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на **SqlSource.** | Да |
| sqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |нет |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |нет |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и корпус параметров должны соответствовать именам и оболочке параметров сохраненной процедуры. |нет |
| isolationLevel | Определяет поведение блокировки транзакции для источника S'L. Разрешенные значения: **ReadCommitted** (по умолчанию), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Снимок**. Для получения более подробной информации обратитесь к [этому документу.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | нет |

**Примечания:**

- Если **sqlReader'ry** указан для **SqlSource,** активность копирования выполняет этот запрос в отношении источника сервера S'L для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если вы не указываете ни **sqlReader,'ry, ни** **sqlReaderStoredProcedureName,** для построения запроса используются столбцы, определенные в разделе "структура" набора данных JSON. Запрос выполняется `select column1, column2 from mytable` в отношении сервера S'L. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример: Используйте запрос S'L**

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

**Пример: Используйте сохраненную процедуру**

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

### <a name="sql-server-as-a-sink"></a>Сервер S'L как раковина

> [!TIP]
> Узнайте больше о поддерживаемых поведениях, конфигурациях и рекомендациях для [загрузок данных в сервер S'L.](#best-practice-for-loading-data-into-sql-server)

Чтобы скопировать данные в базу данных SQL Server, установите тип приемника **SqlSink** в действии копирования. В разделе sink для действия копирования поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **SqlSink.** | Да |
| writeBatchSize |Количество строк для вставки в таблицу S'L *на одну партию.*<br/>Допустимые значения: целое число (количество строк). По умолчанию Azure Data Factory динамически определяет соответствующий размер партии в зависимости от размера строки. |нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Разрешенные значения для периода времени. Примером является "00:30:00" в течение 30 минут. Если значение не указано, тайм-аут по умолчанию до "02:00:00". |нет |
| preCopyScript |В этом свойстве указывается запрос на использование копирования перед записью данных в сервер S'L. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, которые работают только один раз и не имеют ничего `preCopyScript` общего с исходными данными, например, удалить или усечение, используйте свойство. | нет |
| сохраненоProcedureTableTypeNameName |Имя параметра типа таблицы, указанное в сохраненной процедуре.  |нет |
| sqlWriterTableType |Имя типа таблицы, которое будет использоваться в сохраненной процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | нет |
| таблицаВариант | Определяется, следует ли автоматически создавать таблицу раковины, если не существует на основе схемы источника. Автоматическое создание таблицы не поддерживается, когда раковина определяет сохраненную процедуру или постановочную копию настраивается в активности копирования. Разрешенные значения: `none` (по `autoCreate`умолчанию), . |нет |

**Пример 1: Данные приложения**

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

**Пример 2: Вызвать сохраненную процедуру во время копирования**

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Лучшая практика для загрузки данных в сервер S'L

При копировании данных в сервер S'L Server может потребоваться другое поведение записи:

- [Приложение](#append-data): Мои исходные данные имеют только новые записи.
- [Upsert](#upsert-data): Мои исходные данные имеют как вставки, так и обновления.
- [Перезапись](#overwrite-the-entire-table): Я хочу перезагрузить всю таблицу измерения каждый раз.
- [Пишите с пользовательской логикой:](#write-data-with-custom-logic)Мне нужна дополнительная обработка до окончательного вставки в таблицу назначения.

Ознакомьтесь с соответствующими разделами, как настроить их на фабрике данных Azure и рекомендациях.

### <a name="append-data"></a>Добавление данных.

Прилагающие данные — это поведение по умолчанию этого разъема раковины сервера S'L. Фабрика данных Azure делает массовую вставку для эффективной записи на стол. Вы можете настроить источник и утонуть соответственно в активности копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1:** Если у вас есть большой объем данных для копирования, используйте следующий подход, чтобы сделать upsert: 

- Во-первых, используйте [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) для навалом загрузки всех записей с помощью копирования деятельности. Поскольку операции против временных таблиц не регистрируются, можно загрузить миллионы записей за считанные секунды.
- Запустите сохраненную процедуру на фабрике данных Azure, чтобы применить выписку [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или INSERT/UPDATE. Используйте таблицу temp в качестве источника для выполнения всех обновлений или вставок в качестве одной транзакции. Таким образом, уменьшается количество поездок в обход и операций журнала. В конце действия процедуры хранения таблица температуры может быть усечена, чтобы быть готовой к следующему циклу upsert.

Например, на фабрике данных Azure можно создать конвейер с **активностью Copy,** прикованным к **активности Сохраненной процедуры.** Бывшие копирует данные из исходного хранилища во временную таблицу базы данных, например, **#UpsertTempTable**, как имя таблицы в наборе данных. Затем последний вызывает сохраненную процедуру для слияния исходных данных из таблицы временных топоров в целевую таблицу и очистки таблицы временных топор.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

В базе данных определите сохраненную процедуру с логикой MERGE, например следующий пример, на который указывается из предыдущей сохраненной процедуры. Предположим, что целью является таблица **маркетинга** с тремя столбцов: **ProfileID,** **Состояние**и **Категория**. У upsert на основе столбца **ProfileID.**

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

**Вариант 2:** Вы также можете вызвать [сохраненную процедуру в рамках действия копирования.](#invoke-a-stored-procedure-from-a-sql-sink) Этот подход выполняет каждую строку в исходной таблице вместо использования объемной вставки в качестве подхода по умолчанию в деятельности копирования, что не подходит для крупномасштабных upsert.

### <a name="overwrite-the-entire-table"></a>Перезапись всей таблицы

Вы можете настроить свойство **preCopyScript** в раковину активности копирования. В этом случае для каждого действия копирования Azure Data Factory запускает сценарий первым. Затем он запускает копию для вставки данных. Например, чтобы перезаписать всю таблицу с последними данными, укажите сценарий, чтобы сначала удалить все записи, прежде чем навалом загрузить новые данные из источника.

### <a name="write-data-with-custom-logic"></a>Запись данных с помощью пользовательской логики

Шаги для записи данных с помощью пользовательской логики аналогичны тем, которые описаны в разделе [данных Upsert.](#upsert-data) Если вам нужно применить дополнительную обработку до окончательного включения исходных данных в таблицу назначения, для крупного масштаба, вы можете сделать одну из двух вещей: 

- Загрузите на временную таблицу, а затем вызвать сохраненную процедуру. 
- Вызвать сохраненную процедуру во время копирования.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базу данных сервера S'L, вы также можете настроить и вызвать пользовательскую процедуру хранения с дополнительными параметрами. Функция сохраненной процедуры использует [параметры, оцениваемые таблицей.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> Ссылаясь на сохраненную процедуру, обрабатывает строку данных по строке, а не с помощью основной операции, которую мы не рекомендуем для крупномасштабной копии. Узнайте больше из [наилучшей практики для загрузки данных в сервер S'L.](#best-practice-for-loading-data-into-sql-server)

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Например, когда требуется применить дополнительную обработку до окончательного включения исходных данных в таблицу назначения. Некоторые дополнительные примеры обработки, когда вы хотите объединить столбцы, искать дополнительные значения и вставлять данные в более чем одну таблицу.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Server. Предположим, что данные ввода и таблица **маркетинга раковины** имеют по три столбца: **ProfileID,** **State**и **Category**. У upsert на основе столбца **ProfileID,** и только применить его для конкретной категории под названием "ProductA".

1. В базе данных определите тип таблицы с тем же именем, **что и sqlWriterTableType.** Для типа таблицы укажите ту же схему, которая возвращается для входных данных.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. В базе данных определите сохраненную процедуру с тем же именем, **что и sqlWriterStoredProcedureName.** Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа таблицы в сохраненной процедуре такое же, как **и название таблицы,** определенное в наборе данных.

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

3. На фабрике данных Azure определите раздел **раковины S'L** в действии копирования следующим образом:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Отображение типа данных для сервера S'L

При копировании данных с сервера и на сервере S'L используются следующие отображения от типов данных сервера S'L Server до промежуточных типов данных Azure Data Factory. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных SQL Server | Промежуточный тип данных Фабрики данных Azure |
|:--- |:--- |
| BIGINT |Int64 |
| binary |Byte[] |
| bit |Логическое |
| char |String, Char[] |
| Дата |Дата и время |
| Datetime |Дата и время |
| datetime2 |Дата и время |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| Изображение |Byte[] |
| INT |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| nvarchar |String, Char[] |
| real |Один |
| rowversion |Byte[] |
| smalldatetime |Дата и время |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Объект |
| text |String, Char[] |
| time |TimeSpan |
| TIMESTAMP |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. Если для ваших данных требуется точность больше 28, попробуйте преобразовать их в строковые данные в SQL-запросе.

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Свойства активности GetMetadata

Чтобы узнать подробности о свойствах, проверьте [активность GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Устранение неполадок с подключением

1. Нависла на экземпляр s'L Server, чтобы принять удаленные соединения. Запустите **студию управления серверами S'L,** **сервер**с правом щелчками мыши и выберите **Свойства.** Выберите **соединения** из списка и выберите **удаленное подключение Allow к этому серверу.**

    ![Включение удаленных подключений](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Для подробных шагов [см.](https://msdn.microsoft.com/library/ms191464.aspx)

2. Запустите **менеджер конфигурации сервера S'L**. Разверните узел **Сетевая конфигурация SQL Server** для нужного экземпляра и выберите пункт **Protocols for MSSQLSERVER** (Протоколы для MSSQLSERVER). Протоколы отображаются в правом стене. Включить TCP/IP путем нажатия правой кнопки **TCP/IP** и выбора **Enable**.

    ![Включение TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Для получения дополнительной информации и альтернативных способов включения протокола TCP/IP [см.](https://msdn.microsoft.com/library/ms191294.aspx)

3. В том же окне, дважды щелкните **TCP/IP** для запуска окна **TCP/IP Properties.**
4. Переключитесь на вкладку **IP-адресов.** Прокрутите вниз, чтобы увидеть раздел **IPAll.** Запишите **порт TCP**. По умолчанию **1433**.
5. Создайте на компьютере **правило брандмауэра Windows** , чтобы разрешить входящий трафик через этот порт. 
6. **Проверить подключение к**серверу S'L Server с помощью полностью квалифицированного имени, используйте студию управления серверами s'L с другой машины. Например, `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Дальнейшие действия
Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
