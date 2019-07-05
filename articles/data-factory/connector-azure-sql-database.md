---
title: Копирование данных в базу данных SQL Azure и из нее с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в базу данных SQL Azure или из базы данных SQL в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449604"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию фабрики данных Azure, которую вы используете:"]
> * [Версия 1](v1/data-factory-azure-sql-connector.md)
> * [Текущая версия](connector-azure-sql-database.md)

В этой статье описывается, как копировать данные из базы данных SQL Azure. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель базы данных SQL Azure поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживается источника и приемника матрицы](copy-activity-overview.md) таблицы
- [Процесс сопоставления данных](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Этот соединитель Базы данных SQL Azure поддерживает такие функции:

- Копирование данных с помощью проверки подлинности SQL и маркера проверки подлинности приложения Azure Active Directory (Azure AD) с удостоверениями, участника или управляемые службы для ресурсов Azure.
- В качестве источника, извлечение данных с помощью SQL-запроса или хранимой процедуры.
- Как приемник, добавление данных в целевую таблицу или вызов хранимой процедуры с помощью пользовательской логики во время копирования.

>[!NOTE]
>База данных SQL Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) теперь не поддерживается этим соединителем. Чтобы обойти это, можно использовать [универсальный соединитель ODBC](connector-odbc.md) и драйвер ODBC для SQL Server с помощью локальной среды выполнения интеграции. Выполните [в этом руководстве](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) с ODBC драйвер загрузки и подключения строки конфигурации.

> [!IMPORTANT]
> При копировании данных с помощью среды выполнения интеграции фабрики данных Azure, настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) служб Azure можно получить доступ к серверу.
> При копировании данных с использованием среды выполнения интеграции, настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. Этот диапазон включает IP-адреса компьютера, который используется для подключения к базе данных SQL Azure.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю базы данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Базы данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **AzureSqlDatabase**. | Yes |
| connectionString | В свойстве **connectionString** указываются сведения, необходимые для подключения к экземпляру Базы данных SQL Azure. <br/>Пометьте это поле как **SecureString** чтобы безопасно хранить его в фабрике данных Azure. Также можно поместить ключ субъекта-службы или пароля в Azure Key Vault. Если проверка подлинности SQL, на включение внесенных изменений `password` настройку из строки подключения. Дополнительные сведения см. в примере JSON, в следующей таблице и [Store учетные данные в хранилище ключей Azure](store-credentials-in-key-vault.md). | Да |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString** чтобы безопасно хранить его в фабрике данных Azure или [ссылка на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| клиент | Укажите информацию о клиенте, такие как имя домена или идентификатор, на котором размещается приложение клиента. Его получить, наведя указатель мыши в правом верхнем углу портала Azure. | Да, при использовании проверки подлинности Azure AD с субъектом-службой |
| connectVia | Это [среда выполнения интеграции](concepts-integration-runtime.md) для подключения к хранилищу данных. Если хранилище данных находится в частной сети можно использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции. Если не указан, используется среда выполнения интеграции Azure по умолчанию. | Нет |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- [Аутентификация по маркеру безопасности приложения Azure AD на основе субъекта-службы](#service-principal-authentication).
- [Аутентификация по маркеру безопасности приложения Azure AD на основе управляемых удостоверений для ресурсов Azure](#managed-identity).

>[!TIP]
>Если вы достигнете ошибку с кодом ошибки «UserErrorFailedToConnectToSqlServer», а также сообщение, как «ограничение сеансов для базы данных — XXX и был достигнут», добавить `Pooling=false` строку подключения и повторите попытку.

### <a name="sql-authentication"></a>Аутентификация SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Пароль в Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Чтобы использовать проверку подлинности по маркеру приложения Azure AD на основе субъекта-службы, выполните следующие действия:

1. [Создание приложения Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. [Подготовка администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) для сервера SQL Azure на портале Azure, если вы еще этого не сделали. Администратор Azure AD должен быть пользователем Azure AD или группой Azure AD, но он не может быть субъектом-службой. Этот шаг нужен, чтобы затем можно было использовать удостоверение Azure AD для создания пользователя автономной базы данных для субъекта-службы.

3. [Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для субъекта-службы. Подключиться к базе данных из или к которой вы хотите копировать данные с помощью таких средств, как SQL Server Management Studio с помощью удостоверения Azure AD, который имеет по крайней мере разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Предоставьте субъекту-службе необходимые разрешения, как обычно для пользователей SQL или другим пользователям. Выполните следующий код. Дополнительные параметры, см. в разделе [в этом документе](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Настройте службу связанной базы данных SQL Azure в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет отдельную фабрику данных. Это управляемое удостоверение можно использовать для проверки подлинности базы данных SQL Azure. Назначенная фабрика может получить доступ и скопировать данные из вашей базы данных или в нее, используя этот идентификатор.

Чтобы использовать управляемое удостоверение проверки подлинности, выполните следующие действия.

1. [Подготовка администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) для сервера SQL Azure на портале Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группу Azure AD. При предоставлении группе с управляемыми удостоверениями роль администратора, пропустите шаги 3 и 4. Администратор имеет полный доступ к базе данных.

2. [Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) для фабрики данных Azure управляемое удостоверение. Подключиться к базе данных из или к которой вы хотите копировать данные с помощью таких средств, как SQL Server Management Studio с помощью удостоверения Azure AD, который имеет по крайней мере разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Предоставьте фабрики данных, управляемых удостоверений необходимые разрешения, что вы предоставляете и другим пользователям SQL. Выполните следующий код. Дополнительные параметры, см. в разделе [в этом документе](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Настройте службу связанной базы данных SQL Azure в фабрике данных Azure.

**Пример**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в разделе [наборы данных](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Этот раздел содержит список свойств, поддерживаемых набором данных Базы данных SQL Azure.

Чтобы скопировать данные из или в базу данных SQL Azure, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство **type** для набора данных должно иметь значение: **AzureSqlTable**. | Yes |
| tableName | Имя таблицы или представления в экземпляре Базы данных SQL Azure, на которое ссылается связанная служба. | "Нет" для источника, "Да" для приемника |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, доступных для определения действий, см. в разделе [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником базы данных SQL Azure.

### <a name="azure-sql-database-as-the-source"></a>База данных SQL Azure в качестве источника

Чтобы скопировать данные из базы данных SQL Azure, установите **тип** свойства в источнике действия копирования для **SqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | **Тип** свойство источника действия копирования должно быть присвоено **SqlSource**. | Да |
| sqlReaderQuery | Это свойство применяет пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать имена и регистром параметров хранимой процедуры. | Нет |

**Примечания:**

- Если **sqlReaderQuery** указывается для **SqlSource**, действие копирования выполняет этот запрос для источника базы данных SQL Azure для получения данных. Есть и другой вариант: создать хранимую процедуру, указав ее имя в **sqlReaderStoredProcedureName** и параметры в **storedProcedureParameters**, если она принимает параметры.
- Если вы не укажете либо **sqlReaderQuery** или **sqlReaderStoredProcedureName**, столбцы, определенные в разделе «structure» набора данных JSON используются для создания запроса. Запрос `select column1, column2 from mytable` работает с базой данных SQL Azure. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.

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
> Дополнительные сведения о поддерживаемых записи поведения, конфигурации и передовом опыте [рекомендация по загрузке данных в базе данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database).

Чтобы скопировать данные в базу данных SQL Azure, установите **тип** свойство в действии копирования приемника для **SqlSink**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | **Тип** свойства приемника действия копирования должно быть присвоено **SqlSink**. | Да |
| writeBatchSize | Число строк, которые вставляются в таблицу SQL *в пакете*.<br/> Допустимое значение: **целое число** (количество строк). По умолчанию фабрика данных Azure динамически определяет размер соответствующего пакета, в зависимости от размера строки. | Нет |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/> Допустимое значение — **timespan**. Например, «00: 30:00» (30 минут). | Нет |
| preCopyScript | Укажите SQL-запрос для действия копирования для запуска перед записью данных в базе данных SQL Azure. Он вызывается однократно при каждом запуске копирования. Это свойство используется для очистки предварительно загруженных данных. | Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет *вызываться для каждого пакета*. Для операций, которые выполняются только один раз и не имеют ничего общего с исходным данным, например, удалить или усечь, использовать `preCopyScript` свойство. | Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| sqlWriterTableType | Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Это позволяет при выполнении хранимой процедуры объединить копируемые и существующие данные. | Нет |

**Пример 1. Добавление данных**.

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Рекомендации по загрузке данных в базе данных SQL Azure

При копировании данных в базу данных SQL Azure, может потребоваться поведение разных записи:

- [Добавление](#append-data): Источник данных имеет только новые записи.
- [Upsert](#upsert-data): Источник данных имеет вставок и обновлений.
- [Перезаписать](#overwrite-the-entire-table): Я хочу повторно загрузить таблицу целиком измерения каждый раз.
- [Записи с помощью пользовательской логики](#write-data-with-custom-logic): Требуется дополнительная обработка до окончательной вставки в целевую таблицу.

Ссылки на соответствующие разделы о том, как настроить в фабрике данных Azure и рекомендации.

### <a name="append-data"></a>Добавление данных

Добавление данных является поведением по умолчанию этого соединителя базы данных SQL Azure в качестве приемника. Фабрика данных выполняет массовую вставку на эффективную запись в таблицу. Можно настроить источник и приемник соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Вариант 1.** При наличии большой объем данных для копирования, для выполнения операции upsert использовать следующий подход: 

- Во-первых, используйте [временной таблицы в области базы данных](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) для массовой загрузки, все записи с помощью действия копирования. Так как не вошли операций для временных таблиц уровня базы данных, можно загрузить из миллионов записей в секундах.
- Выполните действие хранимой процедуры в фабрике данных Azure для применения [СЛИЯНИЯ](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) или инструкции INSERT/UPDATE. Используйте временную таблицу, как источник для выполнения всех обновляет или вставляет в виде одной транзакции. Таким образом уменьшается количество циклов и журнал операций. В конце действия хранимой процедуры временная таблица может быть усечен для обеспечения готовности к следующего цикла upsert.

В качестве примера в фабрике данных Azure, вы можете создать конвейер с **действие копирования** цепочке с **действия хранимой процедуры**. Первый копирует данные из исходного хранилища в базе данных SQL временную таблицу, к примеру, **##UpsertTempTable**, как имя таблицы в наборе данных. Затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевую таблицу и очистки временной таблицы.

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

- Загрузить базы данных с областью во временную таблицу и затем вызвать хранимую процедуру. 
- Вызовите хранимую процедуру во время копирования.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базу данных SQL Azure, также можно настроить и вызвать хранимую процедуру с дополнительными параметрами, определяемое пользователем.

> [!TIP]
> Вызов хранимой процедуры обрабатывает данные построчно вместо с использованием массовой операции, которой мы не рекомендуем для крупномасштабного копирования. Дополнительные сведения из [рекомендация по загрузке данных в базе данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database).

Вы можете использовать хранимую процедуру, когда встроенные механизмы копирования не подходят. Например, если вы хотите применить дополнительной обработки до окончательной вставки данных источника в целевую таблицу. Примеры дополнительной обработки — Если вы хотите объединить столбцы, поиск дополнительных значений и вставки в более чем одна таблица.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Azure. Предполагается, что входные данные и приемником **маркетинга** каждая таблица имеет три столбца: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

**Выходной набор данных:** «tableName» — это то же имя параметра типа таблицы в хранимой процедуре, как показано в следующем сценарии хранимая процедура:

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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

В этой хранимой процедуре используются [параметры с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

Дополнительные сведения см. в [преобразование исходного](data-flow-source.md) и [приемника преобразования](data-flow-sink.md) в сопоставление потока данных.

## <a name="data-type-mapping-for-azure-sql-database"></a>Сопоставление типов данных для Базы данных SQL Azure

При копировании данных из или в базу данных SQL Azure используются следующие сопоставления из типов данных базы данных SQL Azure для промежуточных типов данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных в Базе данных SQL Azure | Промежуточный тип данных Фабрики данных Azure |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal, Фабрика данных Azure поддерживает точность до 28. При наличии данных с точностью больше 28, рассмотрите возможность преобразования в строку в SQL-запросе.

## <a name="next-steps"></a>Дальнейшие действия
Список хранилищ данных, поддерживаемых в качестве источников и приемников, с помощью действия копирования в фабрике данных Azure, см. в разделе [поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats).
