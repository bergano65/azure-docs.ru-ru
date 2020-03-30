---
title: Копирование данных в и из управляемой базы данных Azure S'L Instance
description: Сведения о перемещении данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238752"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Копирование данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure скопировать данные в Управляемый экземпляр Базы данных SQL Azure и из него. Он основывается на статье [обзора активности Copy,](copy-activity-overview.md) в ней представлен общий обзор действия копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем управляемых экземпляров базы данных Azure S'L поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матрицы источника/раковины](copy-activity-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Данные из Управляемого экземпляра Базы данных SQL Azure можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. И наоборот, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в управляемый экземпляр. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Управляемого экземпляра Базы данных SQL Azure поддерживает:

- Копирование данных с помощью проверки подлинности s'L и проверки маркера тёпов приложения Azure (Azure AD) с помощью основного или управляемого идентификатора для ресурсов Azure.
- В качестве источника, получение данных с помощью запроса S'L или сохраненной процедуры.
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

>[!NOTE]
>Управляемая база данных Azure S'L [Не](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) поддерживается этим разъемом. Для работы можно использовать [универсальный разъем ODBC](connector-odbc.md) и драйвер ODBC сервера S'L с помощью автономной интеграции. Следуйте [этому руководству](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) с загрузкой драйвера ODBC и конфигурациями строки соединения.

## <a name="prerequisites"></a>Предварительные требования

Для доступа к [общедоступной конечной точке](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)управляемых экземпляров базы данных Azure S'L можно использовать управляемый системой обработки данных Azure. Убедитесь, что вы включили конечную точку, а также разрешали общедоступный конечный трафик в группе сетевой безопасности, чтобы Фабрика данных Azure могла подключиться к вашей базе данных. Для получения дополнительной информации смотрите [это руководство](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Чтобы получить доступ к частной конечной точке управления базой данных Azure S'L, навязай [автономное время выполнения интеграции,](create-self-hosted-integration-runtime.md) с помощью которого можно получить доступ к базе данных. Если вы предоставляете автономное время выполнения интеграции в той же виртуальной сети, что и управляемый экземпляр, убедитесь, что машина записи runtime находится в другой подсети, чем управляемый экземпляр. Если вы предоставляете возможность самостоятельной интеграции в другой виртуальной сети, чем управляемый экземпляр, вы можете использовать виртуальную сеть пиринга или виртуальную сеть для виртуального сетевого соединения. Дополнительные сведения см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Фабрики данных Azure, специфичных для разъема управляемых экземпляров управляемых экземпляров базы данных Azure S'L.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Управляемого экземпляра Базы данных SQL поддерживаются следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа должно быть установлено на **AzureSqlMI.** | Да |
| connectionString |В этом свойстве опознавательная информация **о подключении** к управляемому экземпляру с помощью проверки подлинности S'L определяется. Дополнительные сведения представлены в примерах ниже. <br/>По умолчанию используется порт 1433. Если вы используете управляемый экземпляр базы данных Azure S'L с общедоступной конечной точкой, укажите порт 3342.<br> Вы также можете поместить пароль в Хранилище ключей Azure. Если это проверка подлинности S'L, вытащите конфигурацию `password` из строки соединения. Для получения дополнительной информации смотрите пример JSON, следуя за таблицей и [учетными данными хранилища в Azure Key Vault.](store-credentials-in-key-vault.md) |Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| servicePrincipalKey | Укажите ключ приложения. Отметьте это поле как **SecureString** для надежного хранения на фабрике данных Azure или [ссылку на секрет, хранящийся в Хранилище ключей Azure.](store-credentials-in-key-vault.md) | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| tenant | Укажите информацию о арендаторе, например доменное имя или идентификатор клиента, под которым находится приложение. Извлеките его, зависая мышью в правом верхнем углу портала Azure. | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Вы можете использовать самоходном времени выполнения интеграции или время выполнения интеграции Azure, если управляемый экземпляр имеет общедоступную конечную точку и позволяет Фабрике данных Azure получить к нему доступ. Если не указано, используется время выполнения интеграции Azure по умолчанию. |Да |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности СЗЛ](#sql-authentication)
- [Аутентификация по маркеру приложения Azure AD на основе субъекта-службы.](#service-principal-authentication)
- [Аутентификация по маркеру безопасности приложения Azure AD на основе управляемых удостоверений для ресурсов Azure](#managed-identity)

### <a name="sql-authentication"></a>Проверка подлинности SQL

**Пример 1: используйте проверку подлинности S'L**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пример 2: используйте проверку подлинности СЗЛ с паролем в Убежище ключей Azure**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Чтобы использовать проверку подлинности по маркеру приложения Azure AD на основе субъекта-службы, выполните следующие действия:

1. Выполните действия по [предоставлению администратора активного каталога Azure для управляемой инстанции.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. [Создайте приложение Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

3. [Создавайте логины](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) для управляемой итогной фабрики данных Azure. В SQL Server Management Studio (SSMS) подключитесь к управляемому экземпляру с помощью учетной записи SQL Server с ролью **sysadmin**. Запустите следующий код T-SQL для базы данных **master**:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Создание содержащихся пользователей баз данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для управляемой идентификации Фабрики данных Azure. Подключитесь к базе данных, откуда или куда вы хотите скопировать данные, запустите следующий код T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Предоставить управляемой идентификации Data Factory необходимы разрешения, как это обычно делается для пользователей ИЗл и других пользователей. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Настройте службу управления службой управления базой данных Azure S'L в Фабрике данных Azure.

**Пример: использование основной аутентификации службы**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Управляемые идентификаторы для проверки подлинности ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Эту управляемую идентификацию можно использовать для проверки подлинности управляемых экземпляров данных Azure S'L. С помощью этого удостоверения назначенная фабрика может обращаться к данным и копировать их из вашей базы данных или в нее.

Чтобы использовать управляемую проверку подлинности идентификации, выполните следующие действия.

1. Выполните действия по [предоставлению администратора активного каталога Azure для управляемой инстанции.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. [Создавайте логины](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) для управляемой итогной фабрики данных Azure. В SQL Server Management Studio (SSMS) подключитесь к управляемому экземпляру с помощью учетной записи SQL Server с ролью **sysadmin**. Запустите следующий код T-SQL для базы данных **master**:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Создание содержащихся пользователей баз данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для управляемой идентификации Фабрики данных Azure. Подключитесь к базе данных, откуда или куда вы хотите скопировать данные, запустите следующий код T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Предоставить управляемой идентификации Data Factory необходимы разрешения, как это обычно делается для пользователей ИЗл и других пользователей. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Настройте службу управления службой управления базой данных Azure S'L в Фабрике данных Azure.

**Пример: использует управляемую проверку подлинности личных данных**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Для копирования данных в и из управляемой базы данных Azure S'L, поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа набора данных должно быть установлено на **AzureSqlMITable.** | Да |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Название таблицы/просмотра. |"Нет" для источника, "Да" для приемника  |
| tableName | Название таблицы/вид со схемой. Это свойство поддерживается для обратной совместимости. Для новой рабочей `schema` `table`нагрузки, использование и . | "Нет" для источника, "Да" для приемника |

**Пример**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Полный список разделов и свойств, доступных для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Управляемого экземпляра Базы данных SQL.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Управляемый экземпляр Базы данных SQL Azure в качестве источника

Для копирования данных из управляемой базы данных Azure S'L в разделе источника активности копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа источника активности копирования должно быть установлено на **SqlMISource.** | Да |
| sqlReaderQuery |Это свойство применяет пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |нет |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |нет |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |нет |
| isolationLevel | Определяет поведение блокировки транзакции для источника S'L. Разрешенные значения: **ReadCommitted** (по умолчанию), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Снимок**. Для получения более подробной информации обратитесь к [этому документу.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | нет |

**Обратите внимание на следующие моменты.**

- Если **sqlReader's'ry** указан для **SqlMISource,** активность копирования выполняет этот запрос против управляемого источника экземпляра для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если свойства **sqlReaderQuery** или **sqlReaderStoredProcedureName** не указаны, то для построения запроса используются столбцы, определенные в разделе structure шаблона JSON для набора данных. Запрос `select column1, column2 from mytable` выполняется для управляемого экземпляра. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

**Пример: Используйте запрос S'L**

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
                "type": "SqlMISource",
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
                "type": "SqlMISource",
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
> Узнайте больше о поддерживаемых поведениях, конфигурациях и рекомендациях для записи в рекомендациях [по загрузке данных в управляемые данные Azure S'L.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

Для копирования данных в управляемой базе данных Azure S'L в разделе «Поглотитель активности копирования» поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство типа раковины активности копирования должно быть установлено на **SqlMISink.** | Да |
| writeBatchSize |Количество строк для вставки в таблицу S'L *на одну партию.*<br/>Допустимые значения: целое число (количество строк). По умолчанию Azure Data Factory динамически определяет соответствующий размер партии в зависимости от размера строки.  |нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Разрешенные значения для периода времени. Например, 00:30:00 определяет период 30 минут. |нет |
| preCopyScript |В этом свойстве указывается запрос s-L для выполнения действия копирования перед записью данных в управляемый экземпляр. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, которые работают только один раз и не имеют ничего `preCopyScript` общего с исходными данными, например, удалить или усечение, используйте свойство. | нет |
| сохраненоProcedureTableTypeNameName |Имя параметра типа таблицы, указанное в сохраненной процедуре.  |нет |
| sqlWriterTableType |Имя типа таблицы, которое будет использоваться в сохраненной процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | нет |
| таблицаВариант | Определяется, следует ли автоматически создавать таблицу раковины, если не существует на основе схемы источника. Автоматическое создание таблицы не поддерживается, когда раковина определяет сохраненную процедуру или постановочную копию настраивается в активности копирования. Разрешенные значения: `none` (по `autoCreate`умолчанию), . |нет |

**Пример 1: Данные приложения**

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
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Пример 2: Вызвать сохраненную процедуру во время копирования**

Узнайте больше о процедуре [invoke из раковины S'L MI.](#invoke-a-stored-procedure-from-a-sql-sink)

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
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Лучшая практика для загрузки данных в управляемые данные базы данных Azure S'L

При копировании данных в управляемый экземпляр базы данных Azure S'L может потребоваться другое поведение записи:

- [Приложение](#append-data): Мои исходные данные имеют только новые записи.
- [Upsert](#upsert-data): Мои исходные данные имеют как вставки, так и обновления.
- [Перезапись](#overwrite-the-entire-table): Я хочу перезагрузить всю таблицу измерения каждый раз.
- [Пишите с пользовательской логикой:](#write-data-with-custom-logic)Мне нужна дополнительная обработка до окончательного вставки в таблицу назначения. 

Ознакомьтесь с соответствующими разделами, как настроить их на фабрике данных Azure и рекомендациях.

### <a name="append-data"></a>Добавление данных.

Прилагающие данные — это поведение по умолчанию этого разъема раковины управляемых экземпляров базы данных Azure S'L. Фабрика данных Azure делает массовую вставку для эффективной записи на стол. Вы можете настроить источник и утонуть соответственно в активности копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1:** Если у вас есть большой объем данных для копирования, используйте следующий подход, чтобы сделать upsert: 

- Во-первых, используйте [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) для навалом загрузки всех записей с помощью копирования деятельности. Поскольку операции против временных таблиц не регистрируются, можно загрузить миллионы записей за считанные секунды.
- Запустите сохраненную процедуру на фабрике данных Azure, чтобы применить выписку [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или INSERT/UPDATE. Используйте таблицу temp в качестве источника для выполнения всех обновлений или вставок в качестве одной транзакции. Таким образом, уменьшается количество поездок в обход и операций журнала. В конце действия процедуры хранения таблица температуры может быть усечена, чтобы быть готовой к следующему циклу upsert.

Например, на фабрике данных Azure можно создать конвейер с **активностью Copy,** прикованным к **активности Сохраненной процедуры.** Бывшие копирует данные из исходного хранилища во временную таблицу, например, **#UpsertTempTable**, как имя таблицы в наборе данных. Затем последний вызывает сохраненную процедуру для слияния исходных данных из таблицы временных топоров в целевую таблицу и очистки таблицы временных топор.

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

При копировании данных в управляемую базу данных Azure S'L, вы также можете настроить и вызвать процедуру хранения, определяемую пользователем, с дополнительными параметрами. Функция сохраненной процедуры использует [параметры, оцениваемые таблицей.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> Ссылаясь на сохраненную процедуру, обрабатывает строку данных по строке, а не с помощью основной операции, которую мы не рекомендуем для крупномасштабной копии. Узнайте больше из [наилучшей практики для загрузки данных в управляемые данные базы данных Azure S'L.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

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

3. На фабрике данных Azure определите раздел **раковины S'L MI** в активности копирования следующим образом:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Сопоставление типов данных для Управляемого экземпляра Базы данных SQL

При копировании данных в Управляемый экземпляр Базы данных SQL или из него используются следующие сопоставления между типами данных Управляемого экземпляра Базы данных SQL Azure и промежуточными типами данных Фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет схему и типы данных источника и приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных Управляемого экземпляра Базы данных SQL Azure | Промежуточный тип данных Фабрики данных Azure |
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

## <a name="next-steps"></a>Дальнейшие действия
Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
