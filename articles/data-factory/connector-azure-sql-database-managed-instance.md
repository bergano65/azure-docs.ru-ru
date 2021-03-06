---
title: Копирование данных в Управляемый экземпляр Базы данных SQL Azure и из них
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
ms.date: 09/09/2019
ms.openlocfilehash: f1eb8644faf6693a2a33ded489830cf4106df222
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444404"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Копирование данных в Управляемый экземпляр Базы данных SQL Azure и из него с помощью Фабрики данных Azure

В этой статье описывается, как с помощью действия копирования в Фабрике данных Azure скопировать данные в Управляемый экземпляр Базы данных SQL Azure и из него. Это продолжение [обзорной статьи о действии копирования](copy-activity-overview.md), в которой представлены общие сведения об этом действии.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Управляемый экземпляр Базы данных SQL Azure поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой матрицей источника и приемника](copy-activity-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Данные из Управляемого экземпляра Базы данных SQL Azure можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. И наоборот, данные из любого хранилища данных, поддерживаемого в качестве источника, можно скопировать в управляемый экземпляр. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель Управляемого экземпляра Базы данных SQL Azure поддерживает:

- Копирование данных с помощью проверки подлинности SQL и Azure Active Directory (Azure AD) маркера приложения с помощью субъекта-службы или управляемых удостоверений для ресурсов Azure.
- В качестве источника, получая данные с помощью SQL-запроса или хранимой процедуры.
- (в качестве приемника) применение пользовательской логики для добавления данных в целевую таблицу или вызова хранимой процедуры во время копирования.

>[!NOTE]
>Управляемый экземпляр Базы данных SQL Azure [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) сейчас не поддерживается этим соединителем. Для решения этой проблемы можно использовать [универсальный соединитель ODBC](connector-odbc.md) и драйвер SQL Server ODBC через локальную среду выполнения интеграции. Следуйте указаниям [по](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) загрузке драйвера ODBC и настройке строки подключения.

>[!NOTE]
>Субъект-служба и проверки подлинности управляемых удостоверений в настоящее время не поддерживаются этим соединителем. Чтобы решить эту проблемы, выберите соединитель базы данных SQL Azure и укажите сервер управляемого экземпляра вручную.

## <a name="prerequisites"></a>Технические условия

Для доступа к [общедоступной конечной точке](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)управляемый экземпляр базы данных SQL Azure можно использовать управляемую среду выполнения интеграции Azure в фабрике данных Azure. Убедитесь, что включена общедоступная конечная точка, а также разрешите трафик общедоступной конечной точки в группе безопасности сети, чтобы фабрика данных Azure могла подключаться к базе данных. Дополнительные сведения см. в [этом руководстве](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Чтобы получить доступ к закрытой конечной точке Управляемый экземпляр Базы данных SQL Azure, настройте локальную [среду выполнения интеграции](create-self-hosted-integration-runtime.md) , которая может обращаться к базе данных. При подготовке локальной среды выполнения интеграции в той же виртуальной сети, что и управляемый экземпляр, убедитесь, что компьютер среды выполнения интеграции находится в другой подсети, чем управляемый экземпляр. При подготовке локальной среды выполнения интеграции в виртуальной сети, отличной от той, в которой находится управляемый экземпляр, можно использовать пиринг виртуальной сети или виртуальную сеть для подключения к виртуальной сети. Дополнительные сведения см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Начать

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных Azure, относящихся к соединителю Управляемый экземпляр Базы данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Управляемого экземпляра Базы данных SQL поддерживаются следующие свойства.

| Свойство | Description | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства Type необходимо задать значение **азуресклми**. | Да |
| connectionString |Это свойство указывает сведения **ConnectionString** , необходимые для подключения к управляемому экземпляру с помощью проверки подлинности SQL. Дополнительные сведения представлены в примерах ниже. <br/>По умолчанию используется порт 1433. Если вы используете Управляемый экземпляр Базы данных SQL Azure с общедоступной конечной точкой, явно укажите порт 3342.<br> Вы также можете добавить пароль в Azure Key Vault. Если это проверка подлинности SQL, вытяните конфигурацию `password` из строки подключения. Дополнительные сведения см. в примере JSON, который следует за таблицей, и [Храните учетные данные в Azure Key Vault](store-credentials-in-key-vault.md). |Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString** , чтобы безопасно хранить его в фабрике данных Azure, или [сослаться на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| tenant | Укажите сведения о клиенте, например имя домена или идентификатор клиента, в котором находится приложение. Извлеките его, наведя указатель мыши на правый верхний угол портал Azure. | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure, если управляемый экземпляр имеет общедоступную конечную точку и позволяет фабрике данных Azure получить к ней доступ. Если значение не указано, используется среда выполнения интеграции Azure по умолчанию. |Да |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- [Аутентификация по маркеру приложения Azure AD на основе субъекта-службы.](#service-principal-authentication)
- [Аутентификация по маркеру безопасности приложения Azure AD на основе управляемых удостоверений для ресурсов Azure](#managed-identity)

### <a name="sql-authentication"></a>Проверка подлинности SQL

**Пример 1. Использование проверки подлинности SQL**

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

**Пример 2. Использование проверки подлинности SQL с паролем в Azure Key Vault**

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

1. Выполните действия по [подготовке администратора Azure Active Directory для управляемый экземпляр](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Создайте приложение Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) из портал Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

3. [Создайте имена входа](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) для управляемого удостоверения фабрики данных Azure. В SQL Server Management Studio (SSMS) подключитесь к Управляемый экземпляр SQL Server с помощью учетной записи, которая является **администратором**. В базе данных **master** выполните следующий T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для управляемого удостоверения в фабрике данных Azure. Подключитесь к базе данных из или в которую необходимо скопировать данные, выполнив следующий T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Предоставьте управляемому удостоверению фабрики данных необходимые разрешения, как обычно для пользователей SQL и других. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Настройка связанной службы Управляемый экземпляр Базы данных SQL Azure в фабрике данных Azure.

**Пример. Использование проверки подлинности субъекта-службы**

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

### <a name="managed-identity"></a> Управляемые удостоверения для аутентификации ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Это управляемое удостоверение можно использовать для проверки подлинности Управляемый экземпляр Базы данных SQL Azure. С помощью этого удостоверения назначенная фабрика может обращаться к данным и копировать их из вашей базы данных или в нее.

Чтобы использовать аутентификацию управляемого удостоверения, выполните следующие действия.

1. Выполните действия по [подготовке администратора Azure Active Directory для управляемый экземпляр](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Создайте имена входа](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) для управляемого удостоверения фабрики данных Azure. В SQL Server Management Studio (SSMS) подключитесь к Управляемый экземпляр SQL Server с помощью учетной записи, которая является **администратором**. В базе данных **master** выполните следующий T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для управляемого удостоверения в фабрике данных Azure. Подключитесь к базе данных из или в которую необходимо скопировать данные, выполнив следующий T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Предоставьте управляемому удостоверению фабрики данных необходимые разрешения, как обычно для пользователей SQL и других. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Настройка связанной службы Управляемый экземпляр Базы данных SQL Azure в фабрике данных Azure.

**Пример. Использование проверки подлинности управляемого удостоверения**

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

Чтобы скопировать данные в Управляемый экземпляр Базы данных SQL Azure и обратно, поддерживаются следующие свойства:

| Свойство | Description | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type набора данных должно иметь значение **азуресклмитабле**. | Да |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Имя таблицы или представления. |"Нет" для источника, "Да" для приемника  |
| tableName | Имя таблицы или представления со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | "Нет" для источника, "Да" для приемника |

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

Чтобы скопировать данные из Управляемый экземпляр Базы данных SQL Azure, в разделе Источник действия копирования поддерживаются следующие свойства.

| Свойство | Description | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type источника действия копирования должно иметь значение **склмисаурце**. | Да |
| sqlReaderQuery |Это свойство применяет пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. |Нет |
| sqlReaderStoredProcedureName |Это свойство содержит имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет |
| storedProcedureParameters |Это параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет |

**Обратите внимание на следующие моменты.**

- Если для **склмисаурце**указан **sqlReaderQuery** , действие копирования выполняет этот запрос к источнику управляемого экземпляра для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
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

**Пример. использование хранимой процедуры**

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
> Дополнительные сведения о поддерживаемых поведениях записи, конфигурациях и рекомендациях см. в статье рекомендации [по загрузке данных в управляемый экземпляр базы данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Чтобы скопировать данные в Управляемый экземпляр Базы данных SQL Azure, в разделе приемника действия копирования поддерживаются следующие свойства:

| Свойство | Description | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство Type приемника действия копирования должно иметь значение **склмисинк**. | Да |
| writeBatchSize |Число строк, вставляемых в таблицу SQL для *каждого пакета*.<br/>Допустимые значения: целое число (количество строк). По умолчанию фабрика данных Azure динамически определяет соответствующий размер пакета в зависимости от размера строки.  |Нет |
| writeBatchTimeout |Это свойство определяет время ожидания при выполнении операции пакетной вставки, по истечении которого она считается не выполненной.<br/>Допустимые значения — для интервала времени. Например, 00:30:00 определяет период 30 минут. |Нет |
| preCopyScript |Это свойство задает SQL-запрос для выполнения действия копирования перед записью данных в управляемый экземпляр. Он вызывается однократно при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, выполняемых только один раз и не имеющих никаких действий с исходными данными, например Delete или TRUNCATE, используйте свойство `preCopyScript`. | Нет |
| storedProcedureTableTypeParameterName |Имя параметра табличного типа, указанного в хранимой процедуре.  |Нет |
| sqlWriterTableType |Имя типа таблицы, используемое в хранимой процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| таблеоптион | Указывает, следует ли автоматически создавать таблицу приемника, если она не существует на основе исходной схемы. Автоматическое создание таблицы не поддерживается, если приемник указывает, что хранимая процедура или промежуточное копирование настроены в действии копирования. Допустимые значения: `none` (по умолчанию), `autoCreate`. |Нет |

**Пример 1. Добавление данных**

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

**Пример 2. вызов хранимой процедуры во время копирования**

Дополнительные сведения см. в статье [вызов хранимой процедуры из приемника SQL MI](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Рекомендации по загрузке данных в Управляемый экземпляр Базы данных SQL Azure

При копировании данных в Управляемый экземпляр Базы данных SQL Azure может потребоваться другое поведение при записи.

- [Append](#append-data): мои исходные данные имеют только новые записи.
- [Upsert](#upsert-data): исходные данные имеют как вставки, так и обновления.
- [Перезаписать](#overwrite-the-entire-table): я хочу каждый раз загружать всю таблицу измерения.
- [Запись с помощью пользовательской логики](#write-data-with-custom-logic): требуется дополнительные операции перед окончательным вставкой в целевую таблицу. 

Сведения о настройке в фабрике данных Azure и рекомендациях см. в соответствующих разделах.

### <a name="append-data"></a>Добавить данные

Добавление данных — это поведение по умолчанию для соединителя приемника Управляемый экземпляр Базы данных SQL Azure. Фабрика данных Azure обеспечивает эффективную запись в таблицу с помощью операций групповой вставки. Источник и приемник можно настроить соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1.** При наличии большого объема данных для копирования используйте следующий подход для выполнения Upsert: 

- Во-первых, используйте [временную таблицу](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) для выполнения групповой загрузки всех записей с помощью действия копирования. Поскольку операции с временными таблицами не регистрируются, можно загружать миллионы записей за считаные секунды.
- Выполните действие хранимой процедуры в фабрике данных Azure, чтобы применить инструкцию [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или INSERT или Update. Используйте временную таблицу в качестве источника для выполнения всех операций обновления или вставки как одной транзакции. Таким образом уменьшается количество циклов обработки и операций с журналом. В конце действия хранимой процедуры можно усечь временную таблицу, чтобы она была готова к следующему циклу Upsert.

Например, в фабрике данных Azure можно создать конвейер с **действием копирования** , связанным с **действием хранимой процедуры**. Первый копирует данные из исходного хранилища во временную таблицу, например **# #UpsertTempTable**, в качестве имени таблицы в наборе данных. Затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и очистки временной таблицы.

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

При копировании данных в Управляемый экземпляр Базы данных SQL Azure можно также настроить и вызвать указанную пользователем хранимую процедуру с дополнительными параметрами. В этой хранимой процедуре используются [параметры с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> При вызове хранимой процедуры строка данных обрабатывается, а не с помощью массовой операции, которая не рекомендуется для крупномасштабной копии. Узнайте больше о том, как [загружать данные в управляемый экземпляр базы данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

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

2. В базе данных Определите хранимую процедуру с тем же именем, что и **sqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Имя параметра типа таблицы в хранимой процедуре совпадает с именем **TableName** , определенным в наборе данных.

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

3. В фабрике данных Azure Определите раздел **приемника SQL MI** в действии копирования следующим образом:

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
| изображение |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| nvarchar |String, Char[] |
| real |Отдельная |
| rowversion |Byte[] |
| smalldatetime |Дата и время |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Объекты |
| text |String, Char[] |
| time |TimeSpan |
| TIMESTAMP |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. Если для ваших данных требуется точность больше 28, попробуйте преобразовать их в строковые данные в SQL-запросе.

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в Фабрике данных Azure см. в [этой таблице](copy-activity-overview.md##supported-data-stores-and-formats).
