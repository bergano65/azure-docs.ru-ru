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
ms.date: 06/01/2019
ms.author: jingwang
ms.openlocfilehash: 6ae1094a6e47d19af97fbbb1ce988d0756f33731
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048541"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которые можно использовать:"]
> * [Версия 1](v1/data-factory-azure-sql-connector.md)
> * [Текущая версия](connector-azure-sql-database.md)

В этой статье описывается, как копировать данные из базы данных SQL Azure. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель базы данных SQL Azure поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживается источника и приемника матрицы](copy-activity-overview.md) таблицы
- [Процесс сопоставления данных](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Этот соединитель базы данных SQL Azure поддерживает такие функции:

- копирование данных с использованием аутентификации SQL и аутентификации на основе маркеров приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемыми удостоверениями ресурсов Azure;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- добавление данных в целевую таблицу или вызов хранимой процедуры во время копирования с помощью пользовательской логики (в качестве приемника).

База данных SQL Azure с [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) в настоящее время не поддерживается. 

> [!IMPORTANT]
> При копировании данных с использованием среды выполнения интеграции фабрики данных Azure настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он разрешал службам Azure получать доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. В этот диапазон входит IP-адрес компьютера, который используется для подключения к базе данных SQL Azure.

## <a name="get-started"></a>Начало работы

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для соединителя базы данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Для свойства **type** необходимо задать значение **AzureSqlDatabase**. | Yes |
| connectionString | В свойстве **connectionString** указываются сведения, необходимые для подключения к экземпляру базы данных SQL Azure. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить ключ субъекта-службы и пароль в Azure Key Vault и в случае аутентификации SQL получить конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как **SecureString**, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| клиент | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- [Аутентификация по маркеру безопасности приложения Azure AD на основе субъекта-службы](#service-principal-authentication).
- [Аутентификация по маркеру безопасности приложения Azure AD на основе управляемых удостоверений для ресурсов Azure](#managed-identity).

>[!TIP]
>Если вы получили ошибку с кодом ошибки UserErrorFailedToConnectToSqlServer и сообщение типа "Предел сеанса для базы данных — XXX, и он был достигнут", добавьте `Pooling=false` в строку подключения и повторите попытку.

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

**Пароль в Azure Key Vault**. 

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

1. **[Создайте приложение Azure Active Directory на портале Azure](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** . Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратор Azure AD должен быть пользователем Azure AD или группой Azure AD, но он не может быть субъектом-службой. Этот шаг нужен, чтобы затем можно было использовать удостоверение Azure AD для создания пользователя автономной базы данных для субъекта-службы.

3. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к базе данных, из которой или в которую требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код, или см. Дополнительные параметры [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Настройте связанную службу базы данных SQL Azure** в фабрике данных Azure.


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

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратором Azure AD может быть пользователь Azure AD или группа Azure AD. При предоставлении группе с управляемыми удостоверениями роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

2. **[Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  для управляемого удостоверения для фабрики данных. Подключитесь к базе данных, из которой или в которую требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Предоставьте управляемое удостоверение для фабрики данных необходимые разрешения** обычным образом для пользователей SQL и другим пользователям. Выполните следующий код, или см. Дополнительные параметры [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. **Настройте связанную службу базы данных SQL Azure** в фабрике данных Azure.

**Пример.**

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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Этот раздел содержит список свойств, поддерживаемых набором данных базы данных SQL Azure.

Чтобы скопировать данные из или в базу данных SQL Azure, поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство **type** для набора данных должно иметь значение: **AzureSqlTable**. | Yes |
| tableName | Имя таблицы или представления в экземпляре базы данных SQL Azure, на которое ссылается связанная служба. | "Нет" для источника, "Да" для приемника |

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником базы данных SQL Azure.

### <a name="azure-sql-database-as-the-source"></a>База данных SQL Azure в качестве источника

Чтобы скопировать данные из базы данных SQL Azure, задайте для свойства **type** источника в действии копирования значение **SqlSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство **type** источника действия копирования должно иметь значение **SqlSource**. | Yes |
| sqlReaderQuery | Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |

### <a name="points-to-note"></a>Примечания

- Если для **SqlSource** указано **sqlReaderQuery**, то действие копирования выполняет этот запрос к источнику базы данных SQL Azure для получения данных. Кроме того, можно создать хранимую процедуру. Укажите параметр **sqlReaderStoredProcedureName** и **storedProcedureParameters**, если хранимая процедура принимает параметры.
- Если не указать **sqlReaderQuery** или **sqlReaderStoredProcedureName**, то столбцы, определенные в разделе **structure** набора данных JSON, будут использоваться для построения запроса. `select column1, column2 from mytable` выполняется по отношению к базе данных SQL Azure. Если в определении набора данных нет раздела **structure**, выбираются все столбцы из таблицы.

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
> Дополнительные сведения о поддерживаемых записи поведения, конфигурации и рекомендации от [рекомендация по загрузке данных в базе данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database).

Чтобы скопировать данные в базу данных SQL Azure, задайте для свойства **type** приемника в действии копирования значение **SqlSin**. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно для заполнения |
|:--- |:--- |:--- |
| type | Свойство **type** приемника действия копирования должно иметь значение **SqlSink**. | Yes |
| writeBatchSize | Количество строк для вставки в таблицу SQL **в пакете**.<br/> Допустимое значение: **целое число** (количество строк). По умолчанию фабрика данных динамически определяет размер соответствующего пакета, в зависимости от размера строки. | Нет |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/> Допустимое значение — **timespan**. Пример: "00:30:00" (30 минут). | Нет |
| preCopyScript | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в базу данных SQL Azure. Он вызывается однократно при каждом запуске копирования. Это свойство используется для очистки предварительно загруженных данных. | Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. <br/>Эта хранимая процедура будет **вызываться для каждого пакета**. Для операций, которые только один раз и не имеют ничего общего с исходным данным, например, удалить или усечь, использовать `preCopyScript` свойство. | Нет |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| sqlWriterTableType | Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. | Нет |

**Пример 1: Добавление данных**

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

**Пример 2: вызова хранимой процедуры во время копирования**

Дополнительные сведения см. в разделе [Вызов хранимой процедуры из приемника SQL](#invoking-stored-procedure-for-sql-sink).

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

При копировании данных в базу данных SQL Azure, вам может потребоваться поведение разных записи:

- **[Добавление](#append-data)** : источник данных имеет только новых записей;
- **[Upsert](#upsert-data)** : данные источника с вставок и обновлений;
- **[Перезаписать](#overwrite-entire-table)** : Я хочу перезагрузить таблицы всего измерения каждый раз;
- **[Записи с помощью пользовательской логики](#write-data-with-custom-logic)** : Требуется дополнительная обработка до окончательной вставки в целевую таблицу.

См. соответственно разделы по настройке в ADF и рекомендации.

### <a name="append-data"></a>Добавление данных

Это поведение по умолчанию этого приемника соединителя базы данных SQL Azure и выполните ADF **инструкции bulk insert** на эффективную запись в таблицу. Можно просто настроить источник и приемник соответствующим образом в действии копирования.

### <a name="upsert-data"></a>Операция upsert с данными

**Параметр я** (желательно, особенно при наличии больших данных для копирования): **большинство эффективный подход** сделать upsert представляет собой следующее: 

- Во-первых, использовать [временной таблицы в области базы данных](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) выполнить массовую загрузку всех записей с помощью действия копирования. Как операции с базой данных с заданной областью временные таблицы не регистрируются, можно загрузить из миллионов записей в секундах.
- Выполнение действия хранимой процедуры в ADF, чтобы применить [СЛИЯНИЯ](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (или вставки или обновления) инструкции и используйте temp таблицы как источник для выполнения всех обновляет или вставляет в виде одной транзакции, уменьшая объем циклические операции и записи операций. В конце действия хранимой процедуры временной таблицы может быть усечен для обеспечения готовности к следующего цикла upsert. 

В качестве примера в фабрике данных Azure, вы можете создать конвейер с **действие копирования** цепочке с **действия хранимой процедуры** при успешном выполнении. Первый копирует данные из исходного хранилища в базе данных SQL временную таблицу, скажем « **##UpsertTempTable**"как имя таблицы в наборе данных, затем второй вызывает хранимую процедуру для слияния исходных данных из временной таблицы в целевой объект таблицы и очистка временной таблицы.

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

Примерно так, как описано в разделе [данных Upsert](#upsert-data) раздел, если вам нужно применить дополнительной обработки до окончательной вставки данных источника в целевую таблицу, вы можете) для больших объемов загрузки базы данных с областью во временную таблицу, затем вызвать Хранимая процедура, или б) вызов хранимой процедуры во время копирования.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

При копировании данных в базу данных SQL Azure, можно также настроить и вызвать хранимую процедуру с дополнительными параметрами, определяемое пользователем.

> [!TIP]
> Вызов хранимой процедуры обрабатывает данные по строкам вместо массовой операции, которая не рекомендуется использовать для копирования большого объема. Дополнительные сведения из [рекомендация по загрузке данных в базе данных SQL Azure](#best-practice-for-loading-data-into-azure-sql-database).

Можно использовать хранимую процедуру, когда встроенные механизмы копирования не подходят, например Применение дополнительной обработки до окончательной вставки данных источника в целевую таблицу. Некоторые дополнительные примеры обработки: объединение столбцов, просмотр дополнительных значений и вставка в несколько таблиц.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Azure. Предположим, что и входные данные, и таблица **Marketing** приемника состоят из трех столбцов: **ProfileID**, **State** и **Category**. Выполните операцию Upsert на основе данных столбца **ProfileID** только для определенной категории.

**Выходной набор данных:** «tableName» должно быть то же имя параметра типа таблицы в хранимой процедуре (см. ниже скрипт хранимой процедуры).

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

В своей базе данных определите тип таблицы с тем же именем, что и **sqlWriterTableType**. Схема типа таблицы должна быть той же, что и схема, возвращаемая входными данными.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

Дополнительные сведения см. в [преобразование исходного](data-flow-source.md) и [приемника преобразования](data-flow-sink.md) в сопоставление потока данных.

## <a name="data-type-mapping-for-azure-sql-database"></a>Сопоставление типов данных для базы данных SQL Azure

При копировании данных в базу данных SQL Azure или из нее используются следующие сопоставления между типами данных базы данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных базы данных SQL Azure | Тип промежуточных данных фабрики данных |
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
| Xml |Xml |

>[!NOTE]
> В настоящее время для типов данных, которые сопоставляются с промежуточными типом Decimal (десятичное число), Фабрика данных Azure поддерживает точность до 28. При наличии данных с точностью больше 28 их можно преобразовать в строку в SQL-запросе.

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
