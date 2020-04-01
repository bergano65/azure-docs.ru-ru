---
title: Копирование и преобразование данных в базе данных Azure S'L
description: Узнайте, как копировать данные в базу данных Azure s'L и из нее, а также преобразовывать данные в базу данных Azure S'L с помощью Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 8f5065a0f4a2a96a747a45f64e00e86f7990bfb8
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437792"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Копирование и преобразование данных в базе данных Azure S'L с помощью Azure Data Factory

> [!div class="op_single_selector" title1="Выберите используемую версию фабрики данных Azure:"]
> * [Версия 1](v1/data-factory-azure-sql-connector.md)
> * [Текущая версия](connector-azure-sql-database.md)

В этой статье излагается, как использовать активность копирования в фабрике данных Azure для копирования данных из базы данных Azure S'L и использования потока данных для преобразования данных в базу данных Azure S'L. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем базы данных Azure S'L поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матричной таблицы источника/раковины](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Для деятельности Copy этот разъем базы данных Azure S'L поддерживает следующие функции:

- Копирование данных с помощью проверки подлинности s'L и проверки маркера тёпов приложения Azure (Azure AD) с помощью основного или управляемого идентификатора для ресурсов Azure.
- В качестве источника, получение данных с помощью запроса S'L или сохраненной процедуры.
- Как раковина, припиская данные к таблице назначения или ссылаясь на сохраненную процедуру с пользовательской логикой во время копии.

>[!NOTE]
>База данных Azure S'L [Всегда зашифрована,](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) не поддерживается этим разъемом. Для работы можно использовать [универсальный разъем ODBC](connector-odbc.md) и драйвер ODBC сервера S'L с помощью автономной интеграции. Следуйте [этому руководству](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) с загрузкой драйвера ODBC и конфигурациями строки соединения.

> [!IMPORTANT]
> Если вы копируете данные с помощью времени выполнения интеграции Azure Data Factory, назначаем [брандмауэр Azure S'L Server,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) чтобы службы Azure могли получить доступ к серверу.
> Если вы копируете данные с помощью автономной интеграции времени выполнения, настроить брандмауэр Azure S'L Server, чтобы обеспечить соответствующий диапазон IP. Этот диапазон включает в себя IP-адрес машины, который используется для подключения к базе данных Azure S'L.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, используемых для определения сущностей Фабрики данных Azure, специфичных для разъема базы данных Azure S'L.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Базы данных SQL Azure поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **AzureSqlDatabase**. | Да |
| connectionString | Укажите информацию, необходимую для подключения к экземпляру базы данных Azure S'L для свойства **connectionString.** <br/>Вы также можете поместить пароль или основной ключ службы в Хранилище ключей Azure. Если это проверка подлинности S'L, вытащите конфигурацию `password` из строки соединения. Для получения дополнительной информации смотрите пример JSON, следуя за таблицей и [учетными данными хранилища в Azure Key Vault.](store-credentials-in-key-vault.md) | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| servicePrincipalKey | Укажите ключ приложения. Отметьте это поле как **SecureString** для надежного хранения на фабрике данных Azure или [ссылку на секрет, хранящийся в Хранилище ключей Azure.](store-credentials-in-key-vault.md) | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| tenant | Укажите информацию о арендаторе, например доменное имя или идентификатор клиента, под которым находится приложение. Извлеките его, зависая мышью в правом верхнем углу портала Azure. | Да, при использовании аутентификации Azure AD с помощью основного сервиса |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Вы можете использовать время выполнения интеграции Azure или самохозня, если ваш хранилище данных находится в частной сети. Если не указано, используется время выполнения интеграции Azure по умолчанию. | Нет |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности СЗЛ](#sql-authentication)
- [Аутентификация по маркеру приложения Azure AD на основе субъекта-службы.](#service-principal-authentication)
- [Аутентификация по маркеру безопасности приложения Azure AD на основе управляемых удостоверений для ресурсов Azure](#managed-identity)

>[!TIP]
>Если вы нажмете на ошибку с кодом ошибки "UserErrorFailedToConnectToSqLServer" и сообщением типа `Pooling=false` "Лимит сеанса для базы данных XXX и был достигнут", добавьте строку подключения и повторите попытку.

### <a name="sql-authentication"></a>Проверка подлинности SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пароль в Хранилище ключей Azure** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. [Создайте приложение Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. Предоставить на портале Azure [Active Directory администратора каталога Azure,](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) если вы еще этого не сделали. Администратор Azure AD должен быть пользователем Azure AD или группой Azure AD, но он не может быть субъектом-службой. Этот шаг нужен, чтобы затем можно было использовать удостоверение Azure AD для создания пользователя автономной базы данных для субъекта-службы.

3. [Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для субъекта-службы. Подключитесь к базе данных, с которой вы хотите скопировать данные с помощью таких инструментов, как S'L Server Management Studio, с идентификатором Azure AD, который имеет по крайней мере любое разрешение USER. Выполните следующий код T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Предоставьте субъекту-службе необходимые разрешения точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Настройте связанную службу Базы данных SQL Azure в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Эту управляемую идентификацию можно использовать для проверки подлинности базы данных Azure s'L. С помощью этого удостоверения назначенная фабрика может обращаться к данным и копировать их из вашей базы данных или в нее.

Чтобы использовать управляемую проверку подлинности идентификации, выполните следующие действия.

1. Предоставить на портале Azure [Active Directory администратора каталога Azure,](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор имеет полный доступ к базе данных.

2. [Создание содержащихся пользователей баз данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для управляемой идентификации Фабрики данных Azure. Подключитесь к базе данных, с которой вы хотите скопировать данные с помощью таких инструментов, как S'L Server Management Studio, с идентификатором Azure AD, который имеет по крайней мере любое разрешение USER. Выполните следующий код T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Предоставить управляемой идентификации Data Factory необходимы разрешения, как это обычно делается для пользователей ИЗл и других пользователей. Выполните следующий код. Дополнительные параметры см. в [этом документе](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Настройте связанную службу Базы данных SQL Azure в фабрике данных Azure.

**Пример**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, [см.](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) 

Следующие свойства поддерживаются в наборе данных базы данных Azure S'L:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** набора данных должно быть установлено на **AzureSqlTable.** | Да |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Название таблицы/просмотра. |"Нет" для источника, "Да" для приемника  |
| tableName | Название таблицы/вид со схемой. Это свойство поддерживается для обратной совместимости. Для новой рабочей `schema` `table`нагрузки, использование и . | "Нет" для источника, "Да" для приемника |

#### <a name="dataset-properties-example"></a>Пример свойств набора данных

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником базы данных SQL Azure.

### <a name="azure-sql-database-as-the-source"></a>База данных SQL Azure в качестве источника

Для копирования данных из базы данных Azure S'L в разделе **источника активности** копирования поддерживаются следующие свойства:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** источника активности копирования должно быть установлено на **AzureSqlSource.** Тип "SqlSource" по-прежнему поддерживается для обратной совместимости. | Да |
| sqlReaderQuery | Это свойство применяет пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и корпус параметров должны соответствовать именам и оболочке параметров сохраненной процедуры. | Нет |
| isolationLevel | Определяет поведение блокировки транзакции для источника S'L. Разрешенные значения: **ReadCommitted** (по умолчанию), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Снимок**. Для получения более подробной информации обратитесь к [этому документу.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Нет |

**Примечания:**

- Если **sqlReader'ruу** указан для **AzureSqlSource,** то действие копий выполняет этот запрос в отношении источника базы данных Azure S'L для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если вы не указываете ни **sqlReader,'ry, ни** **sqlReaderStoredProcedureName,** для построения запроса используются столбцы, определенные в разделе "структура" набора данных JSON. Запрос выполняется `select column1, column2 from mytable` в отношении базы данных Azure S'L. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

#### <a name="sql-query-example"></a>Пример SQL-запроса

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Пример хранимой процедуры

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>Определение хранимой процедуры

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

### <a name="azure-sql-database-as-the-sink"></a>База данных SQL Azure в качестве приемника

> [!TIP]
> Узнайте больше о поддерживаемых поведениях, конфигурациях и рекомендациях для записи в [базу данных Azure S'L.](#best-practice-for-loading-data-into-azure-sql-database)

Для копирования данных в базу данных Azure S'L, следующие свойства поддерживаются в разделе **раковины** активности копирования:

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Свойство **типа** раковины активности копирования должно быть установлено на **AzureSqlSink.** Тип "SqlSink" по-прежнему поддерживается для обратной совместимости. | Да |
| writeBatchSize | Количество строк для вставки в таблицу S'L *на одну партию.*<br/> Допустимое значение: **целое число** (количество строк). По умолчанию Azure Data Factory динамически определяет соответствующий размер партии в зависимости от размера строки. | Нет |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/> Разрешенное значение - **это промежуток времени.** Примером является "00:30:00" (30 минут). | Нет |
| preCopyScript | Укажите запрос на обновление копирования перед записью данных в базу данных Azure S'L. Он вызывается однократно при каждом запуске копирования. Это свойство используется для очистки предварительно загруженных данных. | Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, которые работают только один раз и не имеют ничего `preCopyScript` общего с исходными данными, например, удалить или усечение, используйте свойство. | Нет |
| сохраненоProcedureTableTypeNameName |Имя параметра типа таблицы, указанное в сохраненной процедуре.  |Нет |
| sqlWriterTableType |Имя типа таблицы, которое будет использоваться в сохраненной процедуре. Действие копирования предоставляет доступ к перемещаемым данным во временной таблице с указанным здесь типом. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. |Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| таблицаВариант | Определяется, следует ли автоматически создавать таблицу раковины, если не существует на основе схемы источника. Автоматическое создание таблицы не поддерживается, когда раковина определяет сохраненную процедуру или постановочную копию настраивается в активности копирования. Разрешенные значения: `none` (по `autoCreate`умолчанию), . |Нет |
| отключитьMetricsCollection | Фабрика данных собирает такие показатели, как DТУ базы данных Azure S'L для оптимизации производительности копий и рекомендаций. Если вы обеспокоены этим `true` поведением, укажите, чтобы выключить его. | Нет (значение по умолчанию — `false`) |

**Пример 1: Данные приложения**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Лучшая практика для загрузки данных в базу данных Azure S'L

При копировании данных в базу данных Azure S'L может потребоваться другое поведение записи:

- [Приложение](#append-data): Мои исходные данные имеют только новые записи.
- [Upsert](#upsert-data): Мои исходные данные имеют как вставки, так и обновления.
- [Перезапись](#overwrite-the-entire-table): Я хочу перезагрузить всю таблицу измерения каждый раз.
- [Пишите с пользовательской логикой:](#write-data-with-custom-logic)Мне нужна дополнительная обработка до окончательного вставки в таблицу назначения.

Обратитесь к соответствующим разделам о том, как настроить сярприз в Azure Data Factory и рекомендациях.

### <a name="append-data"></a>Добавление данных.

Прилагающие данные — это поведение по умолчанию этого разъема раковины базы данных Azure S'L. Фабрика данных Azure делает массовую вставку для эффективной записи на стол. Вы можете настроить источник и утонуть соответственно в активности копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1:** Если у вас есть большой объем данных для копирования, используйте следующий подход, чтобы сделать upsert: 

- Во-первых, используйте [временную таблицу с охватом базы данных,](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) чтобы навалом загрузить все записи с помощью действия копирования. Поскольку операции с временными таблицами с охватом баз данных не регистрируются, можно загрузить миллионы записей за считанные секунды.
- Запустите сохраненную процедуру на фабрике данных Azure, чтобы применить выписку [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или INSERT/UPDATE. Используйте таблицу temp в качестве источника для выполнения всех обновлений или вставок в качестве одной транзакции. Таким образом, уменьшается количество поездок в обход и операций журнала. В конце действия процедуры хранения таблица температуры может быть усечена, чтобы быть готовой к следующему циклу upsert.

Например, на фабрике данных Azure можно создать конвейер с **активностью Copy,** прикованным к **активности Сохраненной процедуры.** Бывшие копирует данные из исходного хранилища во временную таблицу базы данных Azure S'L, например, **#UpsertTempTable**, как имя таблицы в наборе данных. Затем последний вызывает сохраненную процедуру для слияния исходных данных из таблицы временных топоров в целевую таблицу и очистки таблицы временных топор.

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

Вы можете настроить свойство **preCopyScript** в раковине активности копирования. В этом случае для каждого действия копирования Azure Data Factory запускает сценарий первым. Затем он запускает копию для вставки данных. Например, чтобы перезаписать всю таблицу с последними данными, укажите сценарий, чтобы сначала удалить все записи, прежде чем навалом загрузить новые данные из источника.

### <a name="write-data-with-custom-logic"></a>Запись данных с помощью пользовательской логики

Шаги для записи данных с помощью пользовательской логики аналогичны тем, которые описаны в разделе [данных Upsert.](#upsert-data) Если вам нужно применить дополнительную обработку до окончательного включения исходных данных в таблицу назначения, для крупного масштаба, вы можете сделать одну из двух вещей:

- Нагрузка на базу данных scoped временная таблица, а затем вызвать сохраненную процедуру. 
- Вызвать сохраненную процедуру во время копирования.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базу данных Azure S'L, вы также можете настроить и вызвать указанную пользователем процедуру хранения с дополнительными параметрами. Функция сохраненной процедуры использует [параметры, оцениваемые таблицей.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> Ссылаясь на сохраненную процедуру, обрабатывает строку данных по строке, а не с помощью основной операции, которую мы не рекомендуем для крупномасштабной копии. Узнайте больше из [наилучшей практики для загрузки данных в базу данных Azure S'L.](#best-practice-for-loading-data-into-azure-sql-database)

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Например, когда требуется применить дополнительную обработку до окончательного включения исходных данных в таблицу назначения. Некоторые дополнительные примеры обработки, когда вы хотите объединить столбцы, искать дополнительные значения, и вставить в более чем одну таблицу.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Azure. Предположим, что данные ввода и таблица **маркетинга раковины** имеют по три столбца: **ProfileID,** **State**и **Category**. У upsert на основе столбца **ProfileID,** и только применить его для конкретной категории под названием "ProductA".

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

При преобразовании данных в картографируем поток данных можно читать и записывать в таблицы из базы данных Azure S'L. Для получения дополнительной [source transformation](data-flow-source.md) информации [sink transformation](data-flow-sink.md) см.

### <a name="source-transformation"></a>Преобразование источника

Настройки, характерные для базы данных Azure S'L, доступны во вкладке **«Параметры исхода»** преобразования источника. 

**Вход:** Выберите, указываете ли вы свой ```Select * from <table-name>```источник за столом (эквивалент) или вводите пользовательский запрос S'L.

**Запрос**: Если вы выберете запрос в поле ввода, введите запрос для вашего источника. Эта настройка перекрывает любую таблицу, выбранную в наборе данных. **Заказ предложения** не поддерживаются здесь, но вы можете установить полное заявление SELECT FROM. Можно также использовать функции таблицы, определяемые пользователем. **select - от udfGetData()** — это UDF в S'L, который возвращает таблицу. Этот запрос создашн таблицу исходных данных, которую можно использовать в потоке данных. Использование запросов также является отличным способом уменьшения строк для тестирования или поиска. 

* Пример СЗЛ:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Размер пакета**: Введите размер пакета, чтобы куски больших данных в считывания.

**Уровень изоляции**: По умолчанию для источников S'L при картировании потока данных читается незафиксированным. Здесь можно изменить уровень изоляции на одно из следующих значений:
* Фиксированное чтение
* Незафиксированное чтение
* Повторяющаяся операция чтения
* Упорядочиваемый уровень изоляции
* Нет (игнорировать уровень изоляции)

![Уровень изоляции](media/data-flow/isolationlevel.png "Уровень изоляции")

### <a name="sink-transformation"></a>Преобразование раковины

Настройки, характерные для базы данных Azure S'L, доступны во вкладке **«Настройки преобразования раковины».**

**Метод обновления:** Определяет, какие операции разрешены в пункте назначения базы данных. По умолчанию разрешаются только вставки. Для обновления, обновления или удаления строк требуется преобразование строки alter-row для тегов строк для этих действий. Для обновления, upserts и удаляет, ключевые столбцы или столбцы должны быть установлены, чтобы определить, какой ряд изменить.

![Ключевые столбцы](media/data-flow/keycolumn.png "Ключевые столбцы")

Название столбца, которое вы выбираете в качестве ключа, будет использоваться ADF как часть последующего обновления, upsert, удалить. Поэтому необходимо выбрать столбец, который существует в отображении Sink. Если вы хотите не писать значение для этой ключевой колонки, нажмите кнопку "Перейти на написание ключевых столбцов".

**Таблица действий:** Определяет, следует ли воссоздать или удалить все строки из таблицы назначения до написания.
* Нет: никакие действия не будут сделаны к столу.
* Воссоздайте: таблица будет удалена и воссоздана. Требуется при динамическом создании новой таблицы.
* Truncate: Все строки из целевой таблицы будут удалены.

**Размер пакета**: Контролирует, сколько строк пишется в каждом ведре. Большие размеры пакетов улучшают сжатие и оптимизацию памяти, но рискуют из-за исключений памяти при кэшировать данные.

**До и после S'L скрипты**: Введите многолинейные скрипты S'L, которые будут выполняться до (предварительная обработка) и после (после обработки) данные записываются в базу данных Sink

![до и после s-L обработки скриптов](media/data-flow/prepost1.png "Скрипты обработки S'L")

## <a name="data-type-mapping-for-azure-sql-database"></a>Сопоставление типов данных для Базы данных SQL Azure

При копировании данных из или в базу данных Azure S'L используются следующие отображения с типами данных базы данных Azure S'L для промежуточных типов данных Azure Data Factory. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных в Базе данных SQL Azure | Промежуточный тип данных Фабрики данных Azure |
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
| tinyint |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. Если у вас есть данные с точностью более 28, рассмотрите возможность преобразования в строку в запросе S'L.

## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Свойства активности GetMetadata

Чтобы узнать подробности о свойствах, проверьте [активность GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Следующие шаги
Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в результате активности копирования на фабрике данных Azure, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
