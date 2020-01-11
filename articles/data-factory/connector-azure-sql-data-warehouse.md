---
title: Копирование и преобразование данных в Azure синапсе Analytics
description: Узнайте, как копировать данные в Azure синапсе Analytics и обратно, а затем преобразовывать данные в Azure синапсе Analytics с помощью фабрики данных.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: f009b438cb0dc227289d65604d89c11fd382b675
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892965"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование и преобразование данных в Azure синапсе Analytics (ранее — хранилище данных SQL Azure) с помощью фабрики данных Azure 

> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которую вы используете:"]
> * [Версии 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Текущая версия](connector-azure-sql-data-warehouse.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из Azure синапсе Analytics и обратно и использовать поток данных для преобразования данных в Azure Data Lake Storage 2-го поколения. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Azure синапсе Analytics поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой таблицей источника или приемника](copy-activity-overview.md)
- [Поток данных сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Для действия копирования этот соединитель Azure синапсе Analytics поддерживает следующие функции:

- копирование данных с использованием аутентификации SQL и аутентификации на основе маркеров приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемыми удостоверениями ресурсов Azure;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- В качестве приемника Загрузите данные с помощью [polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) или [инструкции Copy](#use-copy-statement) (Предварительная версия) или инструкций INSERT. Для повышения производительности копирования рекомендуется использовать Polybase или инструкцию COPY (Предварительная версия).

> [!IMPORTANT]
> При копировании данных с использованием среды выполнения интеграции фабрики данных Azure настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он разрешал службам Azure получать доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. Этот диапазон включает IP-адрес компьютера, используемый для подключения к Azure синапсе Analytics.

## <a name="get-started"></a>Начать

> [!TIP]
> Чтобы добиться лучшей производительности, используйте Polybase для загрузки данных в Azure синапсе Analytics. Подробные сведения см. в разделе [Использование polybase для загрузки данных в Azure синапсе Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . Пошаговое руководство с вариантом использования см. в статье [Загрузка 1 ТБ в Azure синапсе Analytics в течение 15 минут с помощью фабрики данных Azure](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, определяющих сущности фабрики данных, относящиеся к соединителю Azure синапсе Analytics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure синапсе Analytics поддерживаются следующие свойства:

| Свойство            | Description                                                  | Обязательно для заполнения                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Для свойства type необходимо задать значение **AzureSqlDW**.             | Да                                                          |
| connectionString    | Укажите сведения, необходимые для подключения к экземпляру Azure синапсе Analytics для свойства **ConnectionString** . <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить ключ субъекта-службы и пароль в Azure Key Vault и в случае аутентификации SQL получить конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да                                                          |
| servicePrincipalId  | Укажите идентификатора клиента приложения.                         | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| tenant              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia          | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет                                                           |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- Аутентификация по маркеру приложения Azure AD на основе [субъекта-службы](#service-principal-authentication).
- Аутентификация на основе маркеров приложения Azure AD: [Управляемые удостоверения для ресурсов Azure](#managed-identity)

>[!TIP]
>Если вы получили ошибку с кодом ошибки UserErrorFailedToConnectToSqlServer и сообщение типа "Предел сеанса для базы данных — XXX, и он был достигнут", добавьте `Pooling=false` в строку подключения и повторите попытку.

### <a name="sql-authentication"></a>Проверка подлинности SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Пароль в Azure Key Vault**.

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. **[Создайте приложение Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для своего сервера SQL Azure на портале Microsoft Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

3. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующий код T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код или ознакомьтесь с дополнительными параметрами [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать Polybase для загрузки данных, ознакомьтесь с [разрешениями требуемой базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Настройка связанной службы Azure синапсе Analytics** в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identity"></a> Управляемые удостоверения для аутентификации ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет определенную фабрику. Это управляемое удостоверение можно использовать для проверки подлинности Azure синапсе Analytics. Назначенная фабрика может получить доступ и скопировать данные из вашего хранилища данных или в него, используя этот идентификатор.

Чтобы использовать аутентификацию управляемого удостоверения, выполните следующие действия.

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для своего сервера SQL Azure на портале Microsoft Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

2. **[Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для управляемого удостоверения фабрики данных. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Предоставьте управляемому удостоверению фабрики данных необходимые разрешения** , как обычно для пользователей SQL и других. Выполните следующий код или ознакомьтесь с дополнительными параметрами [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать Polybase для загрузки данных, ознакомьтесь с [разрешениями требуемой базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Настройка связанной службы Azure синапсе Analytics** в фабрике данных Azure.

**Пример**.

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). 

Для набора данных Azure синапсе Analytics поддерживаются следующие свойства:

| Свойство  | Description                                                  | Обязательно для заполнения                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Свойство **type** для набора данных должно иметь значение: **AzureSqlDWTable**. | Да                         |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Имя таблицы или представления. |"Нет" для источника, "Да" для приемника  |
| tableName | Имя таблицы или представления со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | "Нет" для источника, "Да" для приемника |

#### <a name="dataset-properties-example"></a>Пример свойств набора данных

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником и приемником Azure синапсе Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure синапсе Analytics в качестве источника

Чтобы скопировать данные из Azure синапсе Analytics, установите свойство **Type** в источнике действия копирования в значение **SqlDWSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство                     | Description                                                  | Обязательно для заполнения |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Свойство **type** источника действия копирования должно иметь значение **SqlDWSource**. | Да      |
| sqlReaderQuery               | Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. | Нет       |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет       |
| storedProcedureParameters    | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет       |

**Пример. Использование SQL-запроса**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Пример хранимой процедуры:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure синапсе Analytics в качестве приемника

Фабрика данных Azure поддерживает три способа загрузки данных в хранилище данных SQL.

![Параметры копирования приемника хранилища данных SQL](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Использовать Polybase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Использование инструкции COPY (Предварительная версия)](#use-copy-statement)
- Использование инструкции INSERT

Самый быстрый и масштабируемый способ загрузки данных — с помощью [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) или [инструкции Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Предварительная версия).

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство          | Description                                                  | Обязательно для заполнения                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Свойство **type** приемника действия копирования должно иметь значение **SqlDWSink**. | Да                                           |
| allowPolyBase     | Указывает, следует ли использовать Polybase для загрузки данных в хранилище данных SQL. `allowCopyCommand` и `allowPolyBase` не могут одновременно иметь значение true. <br/><br/>Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br/>Применяется при использовании Polybase.     |
| polyBaseSettings  | Группа свойств, которые могут быть заданы, если свойство `allowPolybase` имеет значение **true**. | Нет.<br/>Применяется при использовании Polybase. |
| алловкопикомманд | Указывает, следует ли использовать [инструкцию Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (Предварительная версия) для загрузки данных в хранилище данных SQL. `allowCopyCommand` и `allowPolyBase` не могут одновременно иметь значение true. <br/><br/>Ограничения и сведения см. [в разделе Использование инструкции Copy для загрузки данных в хранилище данных SQL Azure](#use-copy-statement) .<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br>Применяется при использовании копирования. |
| копикоммандсеттингс | Группа свойств, которые могут быть заданы, если свойство `allowCopyCommand` имеет значение TRUE. | Нет.<br/>Применяется при использовании копирования. |
| writeBatchSize    | Число строк для вставки в таблицу SQL **для каждого пакета**.<br/><br/>Допустимое значение: **целое число** (количество строк). По умолчанию фабрика данных динамически определяет соответствующий размер пакета в зависимости от размера строки. | Нет.<br/>Применяется при использовании инструкции INSERT.     |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/><br/>Допустимое значение — **timespan**. Пример: 00:30:00 (30 минут). | Нет.<br/>Применяется при использовании инструкции INSERT.        |
| preCopyScript     | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в хранилище данных SQL Azure при каждом выполнении. Это свойство используется для очистки предварительно загруженных данных. | Нет                                            |
| таблеоптион | Указывает, следует ли автоматически создавать таблицу приемника, если она не существует на основе исходной схемы. Автоматическое создание таблицы не поддерживается, если промежуточное копирование настроено в действии копирования. Допустимые значения: `none` (по умолчанию), `autoCreate`. |Нет |
| дисаблеметриксколлектион | Фабрика данных собирает такие метрики, как DWU хранилища данных SQL для оптимизации производительности копирования и рекомендации. Если вы отвечаете за такое поведение, укажите `true`, чтобы отключить его. | Нет (значение по умолчанию — `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Пример приемника хранилища данных SQL

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.

Использование [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) — это эффективный способ загрузки большого объема данных в Azure синапсе Analytics с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Пошаговое руководство с вариантом использования см. [в статье Загрузка 1 ТБ в Azure синапсе Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Если исходные данные находятся в **большом двоичном объекте Azure, Azure Data Lake Storage 1-го поколения или Azure Data Lake Storage 2-го поколения**, а **Формат является совместимым с polybase**, вы можете использовать действие копирования для непосредственного вызова Polybase, чтобы хранилище данных SQL Azure мог получать данные из источника. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-by-using-polybase)** .
* Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-by-using-polybase)** . Промежуточное копирование также обеспечивает лучшую пропускную способность. Он автоматически преобразует данные в формат, совместимый с Polybase, сохраняет данные в хранилище BLOB-объектов Azure. затем вызывает Polybase для загрузки данных в хранилище данных SQL.

>[!TIP]
>Дополнительные сведения см. в статье рекомендации [по использованию polybase](#best-practices-for-using-polybase).

Следующие параметры Polybase поддерживаются в разделе `polyBaseSettings` в действии копирования:

| Свойство          | Description                                                  | Обязательно для заполнения                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе "Аргументы" раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2 и. т. д. | Нет                                            |
| rejectType        | Указывает, является ли параметр **rejectValue** литеральным или процентным.<br/><br/>Допустимые значения: **Значение** (по умолчанию) и **Процент**. | Нет                                            |
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … | Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault    | Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase извлекает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию).<br><br> | Нет                                            |

### <a name="direct-copy-by-using-polybase"></a>Прямое копирование с помощью PolyBase

Polybase хранилища данных SQL напрямую поддерживает большой двоичный объект Azure, Azure Data Lake Storage 1-го поколения и Azure Data Lake Storage 2-го поколения. Если исходные данные соответствуют критериям, описанным в этом разделе, используйте Polybase для копирования непосредственно из исходного хранилища данных в Azure синапсе Analytics. В противном случае см. сведения в разделе [Промежуточное копирование с помощью PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Чтобы эффективно копировать данные в хранилище данных SQL, Узнайте больше от [фабрики данных Azure, что упрощает и упрощает анализ данных при использовании Data Lake Store с хранилищем данных SQL](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. **Связанная служба источника** имеет следующие типы и методы проверки подлинности:

    | Поддерживаемый тип исходного хранилища данных                             | Поддерживаемый тип проверки подлинности источника                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md)                | Проверка подлинности ключа учетной записи, управляемая идентификация |
    | [Хранилище Azure Data Lake Gen1](connector-azure-data-lake-store.md) | Проверка подлинности субъекта-службы                            |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | Проверка подлинности ключа учетной записи, управляемая идентификация |

    >[!IMPORTANT]
    >Если в службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать управляемую идентификацию аутентификации — см. статью [влияние использования конечных точек службы виртуальной сети в службе хранилища Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Сведения о необходимых конфигурациях в фабрике данных см. в разделе [Проверка подлинности управляемого BLOB-объекта Azure](connector-azure-blob-storage.md#managed-identity) и управляемого [Azure Data Lake Storage 2-го поколения удостоверения](connector-azure-data-lake-storage.md#managed-identity) .

2. **Исходный формат данных** — **Parquet**, **ORC**или **текст с разделителями**, со следующими конфигурациями:

   1. Путь к папке не содержит фильтр с подстановочными знаками.
   2. Имя файла пустое или указывает на один файл. Если в действии копирования указано имя файла с подстановочными знаками, оно может быть только `*` или `*.*`.
   3. `rowDelimiter` **по умолчанию**, **\n**, **\r\n**или **\r**.
   4. `nullValue` принимает значение по умолчанию или задает **пустую строку** (""), а `treatEmptyAsNull` принимает значение по умолчанию или принимает значение true.
   5. `encodingName` по умолчанию принимает значение **UTF-8**.
   6. `quoteChar`, `escapeChar`и `skipLineCount` не указаны. Поддержка Polybase пропускает строку заголовка, которая может быть настроена как `firstRowAsHeader` в ADF.
   7. Параметр `compression` может иметь значение **no compression**, **GZip** или **Deflate**.

3. Если источником является папка, `recursive` в действии копирования должно иметь значение true.

4. не указаны `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`и `modifiedDateTimeEnd`.

>[!NOTE]
>Если источником является папка, то Polybase извлекает файлы из папки и всех ее вложенных папок, а также не извлекает данные из файлов, для которых имя файла начинается с подчеркивания (_) или точки (.), как указано в описании [параметра Location](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Промежуточное копирование с помощью PolyBase

Если исходные данные не совместимы с Polybase, включите копирование данных через промежуточный промежуточный экземпляр хранилища BLOB-объектов Azure (это не может быть хранилище Azure класса Premium). В этом случае фабрика данных Azure автоматически преобразует данные в соответствии с требованиями к формату данных Polybase. Затем он вызывает Polybase для загрузки данных в хранилище данных SQL. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Подробные сведения о копировании данных через промежуточный экземпляр хранилища BLOB-объектов Azure см. в разделе [Промежуточное копирование](copy-activity-performance.md#staged-copy).

Чтобы использовать эту функцию, создайте [связанную службу хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties) , которая ссылается на учетную запись хранения Azure с промежуточным хранилищем BLOB-объектов. Затем укажите `enableStaging` и `stagingSettings` свойства для действия копирования, как показано в следующем коде.

>[!IMPORTANT]
>Если в промежуточной службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать управляемую идентификацию с проверкой подлинности. Ознакомьтесь с [влиянием использования конечных точек службы виртуальной сети в службе хранилища Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Сведения о необходимых конфигурациях в фабрике данных см. в статье [Идентификация с помощью управляемого двоичного объекта Azure](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Советы и рекомендации по использованию PolyBase

В следующих разделах приводятся рекомендации в дополнение к тем, которые были упомянуты в рекомендациях по работе с [Azure синапсе Analytics](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, загружающий данные в хранилище, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли **db_owner**. Чтобы узнать, как это сделать, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Ограничение размера строки и типа данных

Загрузки PolyBase ограничены записями размером менее 1 МБ. Его нельзя использовать для загрузки в ПЕРЕМЕННЫЕ VARCHR (MAX), NVARCHAR (MAX) или VARBINARY (MAX). Дополнительные сведения см. в статье [Ограничения емкости хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если исходные данные имеют записи размером более 1 МБ, можно попробовать вертикально разбить исходные таблицы на несколько небольших. Убедитесь, что максимальный размер каждой записи не превышает предел. Затем можно загрузить небольшие таблицы с помощью Polybase и объединить их в Azure синапсе Analytics.

Кроме того, для данных с такими широкими столбцами можно использовать не Polybase для загрузки данных с помощью ADF, отключив параметр Allow Polybase.

#### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, присвойте более высокий класс ресурсов пользователю, который загружает данные в хранилище данных SQL через PolyBase.

#### <a name="polybase-troubleshooting"></a>Устранение неполадок c PolyBase

**Загрузка в десятичный столбец**

Если исходные данные находятся в текстовом формате или других совместимых с Polybase хранилищах (с использованием промежуточного копирования и Polybase) и содержат пустое значение для загрузки в столбец в хранилище данных SQL, может появиться следующее сообщение об ошибке:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Решение состоит в том, чтобы отменить выбор параметра "**использовать тип по умолчанию**" (false) в приемнике действия копирования — > параметры polybase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" — это собственная конфигурация polybase, которая указывает, как следует выполнять обработку отсутствующих значений в текстовых файлах с разделителями, когда polybase извлекает данные из текстового файла. 

**`tableName` в Azure синапсе Analytics**

В следующей таблице приведены примеры того, как указать свойство **tableName** в наборе данных JSON. В ней показаны несколько сочетаний имен схем и таблиц.

| Схема базы данных | Имя таблицы | Свойство **tableName** в JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable или dbo.MyTable либо [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable или [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] или [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Если вы видите следующую ошибку, это может указывать на неправильное значение свойства **tableName**. Правильные значения для свойства **tableName** в JSON см. в таблице выше.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Столбцы со значениями по умолчанию**

Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Примером может служить таблица с четырьмя столбцами, и один из них определен со значением по умолчанию. Входные данные по-прежнему должны иметь четыре столбца. Если входной набор данных будет содержать три столбца, мы получим ошибку, похожую на следующую:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значение NULL, входные данные большого двоичного объекта для этого столбца могут быть пустыми. Но они не могут отсутствовать во входном наборе данных. Polybase вставляет значение NULL для отсутствующих значений в Azure синапсе Analytics.

## <a name="use-copy-statement"></a>Использование инструкции COPY для загрузки данных в хранилище данных SQL Azure (Предварительная версия)

[Инструкция копирования](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) в хранилище данных SQL (Предварительная версия) напрямую поддерживает загрузку данных из **большого двоичного объекта Azure и Azure Data Lake Storage 2-го поколения**. Если исходные данные соответствуют критериям, описанным в этом разделе, можно выбрать использование инструкции COPY в ADF для загрузки данных в хранилище данных SQL Azure. Фабрика данных Azure проверяет параметры и не выполняет действие копирования, если условия не выполнены.

>[!NOTE]
>В настоящее время фабрика данных поддерживает только копию из совместимых с инструкцией COPY источников, упомянутых ниже.

Инструкция COPY позволяет использовать следующую конфигурацию:

1. **Исходная связанная служба и формат** имеют следующие типы и методы проверки подлинности:

    | Поддерживаемый тип исходного хранилища данных                             | Поддерживаемый формат           | Поддерживаемый тип проверки подлинности источника                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md)                | [Текст с разделителями](format-delimited-text.md)             | Аутентификация ключа учетной записи, проверка подлинности подписанного URL-идентификатора, проверка подлинности с помощью управляемого удостоверения |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Проверка подлинности ключа учетной записи, проверка подписи общего доступа |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Проверка подлинности ключа учетной записи, проверка подписи общего доступа |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | [Текст с разделителями](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Аутентификация ключа учетной записи, аутентификация субъекта-службы, аутентификация управляемого удостоверения |

    >[!IMPORTANT]
    >Если в службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать управляемую идентификацию аутентификации — см. статью [влияние использования конечных точек службы виртуальной сети в службе хранилища Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Сведения о необходимых конфигурациях в фабрике данных см. в разделе [Проверка подлинности управляемого BLOB-объекта Azure](connector-azure-blob-storage.md#managed-identity) и управляемого [Azure Data Lake Storage 2-го поколения удостоверения](connector-azure-data-lake-storage.md#managed-identity) .

2. Параметры формата имеют следующие значения:

   1. Для **Parquet**: `compression` не может содержать **Сжатие**, **привязку**или **gzip**.
   2. Для **ORC**: `compression` не может содержать **Сжатие**, **zlib**или **привязку**.
   3. Для **текста с разделителями**:
      1. `rowDelimiter` явно задан как **одиночный символ** или " **\r\n**", значение по умолчанию не поддерживается.
      2. `nullValue` принимает значение по умолчанию или задает **пустую строку** ("").
      3. `encodingName` по умолчанию принимает значение **UTF-8 или UTF-16**.
      4. `escapeChar` должно быть равно `quoteChar`и не пусто.
      5. `skipLineCount` по умолчанию принимает значение 0.
      6. `compression` не может быть **сжатием** или **gzip**.

3. Если источником является папка, `recursive` в действии копирования должно иметь значение true.

4. не указаны `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`и `modifiedDateTimeEnd`.

В разделе `allowCopyCommand` в действии копирования поддерживаются следующие параметры инструкции COPY:

| Свойство          | Description                                                  | Обязательно для заполнения                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Задает значения по умолчанию для каждого целевого столбца в хранилище данных SQL.  Значения по умолчанию в свойстве перезапишут ограничение по УМОЛЧАНИю, заданное в хранилище данных, а столбец идентификаторов не может иметь значение по умолчанию. | Нет |
| additionalOptions | Дополнительные параметры, которые будут переданы в инструкцию COPY в хранилище данных SQL, непосредственно в предложении WITH [инструкции Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Заключить значение в кавычки, чтобы согласовать с требованиями инструкции COPY. | Нет |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов данных для хранилища данных SQL Azure

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

При преобразовании данных в потоке сопоставления данных можно выполнять чтение и запись в таблицы из Azure синапсе Analytics. Дополнительные сведения см. в разделе Преобразование [исходного кода](data-flow-source.md) и [приемника](data-flow-sink.md) при сопоставлении потоков данных.

### <a name="source-transformation"></a>Преобразование источника

Параметры, относящиеся к Azure синапсе Analytics, доступны на вкладке " **Параметры источника** " преобразования "источник". 

**Входные данные:** Укажите, следует ли указывать источник в таблице (эквивалентно ```Select * from <table-name>```) или введите пользовательский SQL-запрос.

**Запрос**: при выборе запроса в поле ввода введите SQL-запрос для источника. Этот параметр переопределяет любую таблицу, выбранную в наборе данных. Предложения **ORDER BY** не поддерживаются здесь, но можно задать полную инструкцию SELECT FROM. Можно также использовать определяемые пользователем табличные функции. **SELECT * FROM удфжетдата ()** — это определяемая пользователем функция в SQL, возвращающая таблицу. Этот запрос приведет к созданию исходной таблицы, которую можно использовать в потоке данных. Использование запросов также является отличным способом сокращения количества строк для тестирования или поиска. 

* Пример SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Размер пакета**: введите размер пакета для фрагментов больших данных в операциях чтения.

**Уровень изоляции**: значение по умолчанию для источников SQL в потоке данных сопоставления — READ UNCOMMITTED. Здесь можно изменить уровень изоляции на одно из следующих значений:
* Фиксированное чтение
* Незафиксированное чтение
* Повторяющаяся операция чтения
* Упорядочиваемый уровень изоляции
* Нет (пропускать уровень изоляции)

![Уровень изоляции](media/data-flow/isolationlevel.png "Уровень изоляции")

### <a name="sink-transformation"></a>Преобразование приемника

Параметры, относящиеся к Azure синапсе Analytics, доступны на вкладке " **Параметры** " преобразования "приемник".

**Метод обновления:** Определяет, какие операции разрешены в назначении базы данных. По умолчанию разрешены только вставки. Для обновления, Upsert или удаления строк необходимо преобразование «изменение строки» для добавления тегов в строки для этих действий. Для обновлений, операции Upsert и DELETE необходимо задать ключевой столбец или столбцы, чтобы определить, какая строка будет изменена.

**Действие таблицы:** Определяет, следует ли повторно создавать или удалять все строки целевой таблицы перед записью.
* Нет: никаких действий над таблицей не выполняется.
* Повторное создание: таблица будет удалена и создана повторно. Требуется при динамическом создании таблицы.
* Усечение: все строки из целевой таблицы будут удалены.

**Включить промежуточное хранение:** Определяет, следует ли использовать [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) при записи в Azure синапсе Analytics

**Размер пакета**: определяет, сколько строк записывается в каждом контейнере. Более крупные размеры пакетов улучшают сжатие и оптимизацию памяти, но при кэшировании данных возникает риск нехватки памяти.

**Пред-и POST-скрипты SQL**: введите многострочные скрипты SQL, которые будут выполняться до (Предварительная обработка) и после чего в базу данных приемника записываются данные (после обработки).

![пред и POST скрипты обработки SQL](media/data-flow/prepost1.png "Скрипты обработки SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Сопоставление типов данных для Azure синапсе Analytics

При копировании данных из или в Azure синапсе Analytics в качестве промежуточных типов данных фабрики данных Azure используются следующие сопоставления типов данных Azure синапсе Analytics. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

>[!TIP]
>Дополнительные сведения о поддерживаемых типах данных и обходных путях для неподдерживаемых типов см. в статье о [типах данных таблиц в Azure синапсе Analytics](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) .

| Тип данных аналитики Azure синапсе    | Тип промежуточных данных фабрики данных |
| :------------------------------------ | :----------------------------- |
| BIGINT                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Логическое                        |
| char                                  | String, Char[]                 |
| Дата                                  | Дата и время                       |
| Datetime                              | Дата и время                       |
| datetime2                             | Дата и время                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| изображение                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| NUMERIC                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Отдельная                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Дата и время                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| UNIQUEIDENTIFIER                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
