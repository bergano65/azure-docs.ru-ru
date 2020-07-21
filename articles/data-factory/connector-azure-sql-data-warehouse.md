---
title: Копирование и преобразование данных в Azure Synapse Analytics
description: Узнайте, как копировать данные в Azure Synapse Analytics и обратно, а также преобразовывать данные в Azure Synapse Analytics с помощью Фабрики данных.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 5810f9b08d914522f1304e238567c06e87872715
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537737"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование и преобразование данных в Azure Synapse Analytics (ранее — хранилище данных SQL Azure) с помощью Фабрики данных Azure

> [!div class="op_single_selector" title1="Выберите используемую версию службы "Фабрика данных":"]
>
> - [Версия 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Текущая версия](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описано, как копировать данные в Azure Synapse Analytics и обратно с помощью действия копирования в Фабрике данных Azure, а также как преобразовывать данные в Azure Data Lake Storage 2-го поколения с помощью Потока данных. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель Azure Synapse Analytics предназначен для поддержки следующих действий:

- [действия копирования](copy-activity-overview.md) с таблицей [поддерживаемой матрицы источника/приемника](copy-activity-overview.md);
- [потока данных для сопоставления](concepts-data-flow-overview.md);
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Для действия копирования этот соединитель Azure Synapse Analytics поддерживает следующие функции:

- копирование данных с использованием аутентификации SQL и аутентификации на основе маркеров приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемыми удостоверениями ресурсов Azure;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- загрузку данных с помощью технологии [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse), [инструкции COPY](#use-copy-statement) (предварительная версия) или массовой вставки (в качестве приемника). Для повышения производительности копирования рекомендуется использовать PolyBase или инструкцию COPY (предварительная версия). Соединитель также поддерживает автоматическое создание целевой таблицы, если она не существует на основе исходной схемы.

> [!IMPORTANT]
> При копировании данных с помощью Integration Runtime фабрики данных Azure настройте [правило брандмауэра на уровне сервера](../azure-sql/database/firewall-configure.md) , чтобы службы Azure могли получить доступ к [ЛОГИЧЕСКОМу серверу SQL](../azure-sql/database/logical-servers.md)Server.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр таким образом, чтобы он разрешал соответствующий диапазон IP-адресов. В этот диапазон входит IP-адрес компьютера, который используется для подключения к Azure Synapse Analytics.

## <a name="get-started"></a>Начало работы

> [!TIP]
> Для обеспечения лучшей производительности загружайте данные в Azure Synapse Analytics с помощью PolyBase. Дополнительные сведения см. в разделе [Загрузка данных в Azure Synapse Analytics с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в Azure Synapse Analytics в течение 15 минут с помощью Фабрики данных Azure](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах содержатся описания свойств, которые определяют сущности Фабрики данных, относящиеся к соединителю Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Synapse Analytics поддерживаются следующие свойства.

| Свойство            | Описание                                                  | Обязательно                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Для свойства type необходимо задать значение **AzureSqlDW**.             | Да                                                          |
| connectionString    | В свойстве **connectionString** указываются сведения, необходимые для подключения к экземпляру Azure Synapse Analytics. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить ключ субъекта-службы и пароль в Azure Key Vault и в случае аутентификации SQL получить конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да                                                          |
| servicePrincipalId  | Укажите идентификатора клиента приложения.                         | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| tenant              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia          | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | нет                                                           |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- Аутентификация по маркеру приложения Azure AD. [Субъект-служба](#service-principal-authentication)
- Аутентификация по маркеру приложения Azure AD. [Управляемые удостоверения для ресурсов Azure](#managed-identity)

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

1. **[Создайте приложение Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** на портале Azure. Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

   - Идентификатор приложения
   - Ключ приложения
   - Tenant ID

2. **[Предоставьте администратору Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** для сервера в портал Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

3. **[Создайте пользователей автономной базы данных](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующий код T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код или посмотрите другие варианты [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите загружать данные с помощью PolyBase, изучите [необходимые разрешения базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Настройте связанную службу Azure Synapse Analytics** в Фабрике данных Azure.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Управляемые удостоверения для аутентификации ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет определенную фабрику. Это управляемое удостоверение можно использовать для проверки подлинности Azure Synapse Analytics. Назначенная фабрика может получить доступ и скопировать данные из вашего хранилища данных или в него, используя этот идентификатор.

Для использования проверки подлинности по управляемому удостоверению выполните следующие действия.

1. **[Предоставьте администратору Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** для сервера на портал Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

2. **[Создайте пользователей автономной базы данных](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** для управляемого удостоверения Фабрики данных. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Предоставьте управляемому удостоверению Фабрики данных необходимые разрешения**, как это обычно делается для пользователей SQL и других пользователей. Выполните следующий код или посмотрите другие варианты [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите загружать данные с помощью PolyBase, изучите [необходимые разрешения базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Настройте связанную службу Azure Synapse Analytics** в Фабрике данных Azure.

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

Для набора данных Azure Synapse Analytics поддерживаются следующие свойства.

| Свойство  | Описание                                                  | Обязательно                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Свойство **type** для набора данных должно иметь значение: **AzureSqlDWTable**. | Да                         |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Имя таблицы или представления. |"Нет" для источника, "Да" для приемника  |
| tableName | Имя таблицы или представления со схемой. Это свойство поддерживается только для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | "Нет" для источника, "Да" для приемника |

### <a name="dataset-properties-example"></a>Пример свойств набора данных

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых Azure Synapse Analytics как источником и как приемником.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics как источник

Чтобы скопировать данные из Azure Synapse Analytics, задайте для свойства **type** в источнике действия копирования значение **SqlDWSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство                     | Описание                                                  | Обязательно |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Свойство **type** источника действия копирования должно иметь значение **SqlDWSource**. | Да      |
| sqlReaderQuery               | Используйте пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. | Нет       |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет       |
| storedProcedureParameters    | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет       |
| isolationLevel | Задает режим блокировки транзакций для источника данных SQL. Допустимые значения: **ReadCommitted** (по умолчанию), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Дополнительные сведения см. в [этом документе](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel). | Нет |

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics как приемник

Фабрика данных Azure поддерживает три способа загрузки данных в хранилище данных SQL.

![Параметры копирования приемника хранилища данных SQL](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Использование PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [Использование инструкции COPY (предварительная версия)](#use-copy-statement)
- Использование массовой вставки

Самым быстрым и масштабируемым способом загрузки данных является использование [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) или [инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (предварительная версия).

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Свойство **type** приемника действия копирования должно иметь значение **SqlDWSink**. | Да                                           |
| allowPolyBase     | Указывает, следует ли использовать PolyBase для загрузки данных в хранилище данных SQL. Свойства `allowCopyCommand` и `allowPolyBase` не могут одновременно иметь значение true. <br/><br/>Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br/>Применяется при использовании PolyBase.     |
| polyBaseSettings  | Группа свойств, которые можно задать, если свойство `allowPolybase` имеет значение **true**. | Нет.<br/>Применяется при использовании PolyBase. |
| allowCopyCommand | Указывает, следует ли использовать [инструкцию COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (предварительная версия) для загрузки данных в хранилище данных SQL. Свойства `allowCopyCommand` и `allowPolyBase` не могут одновременно иметь значение true. <br/><br/>Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью инструкции COPY](#use-copy-statement).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br>Применяется при использовании инструкции COPY. |
| copyCommandSettings | Группа свойств, которые можно задать, если свойство `allowCopyCommand` имеет значение TRUE. | Нет.<br/>Применяется при использовании инструкции COPY. |
| writeBatchSize    | Число строк для вставки в таблицу SQL **в одном пакете**.<br/><br/>Допустимое значение: **целое число** (количество строк). По умолчанию Фабрика данных динамически определяет соответствующий размер пакета в зависимости от размера строки. | Нет.<br/>Применяется при использовании массовой вставки.     |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/><br/>Допустимое значение — **timespan**. Пример "00:30:00" (30 минут). | Нет.<br/>Применяется при использовании массовой вставки.        |
| preCopyScript     | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в хранилище данных SQL Azure при каждом выполнении. Это свойство используется для очистки предварительно загруженных данных. | Нет                                            |
| tableOption | Указывает, следует ли [автоматически создавать таблицу приемника, если она](copy-activity-overview.md#auto-create-sink-tables) не существует на основе исходной схемы. Автоматическое создание таблицы не поддерживается, если в действии копирования настроено промежуточное копирование. Допустимые значения: `none` (по умолчанию), `autoCreate`. |нет |
| disableMetricsCollection | Фабрика данных собирает такие метрики, как DWU хранилища данных SQL, для оптимизации производительности копирования и предоставления рекомендаций. Если вас не устраивает такое поведение, укажите `true`, чтобы отключить его. | Нет (значение по умолчанию — `false`) |

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

Применение [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) — это эффективный способ загрузки большого объема данных в Azure Synapse Analytics с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Если исходные данные находятся в **BLOB-объекте Azure, Azure Data Lake Storage 1-го поколения или Azure Data Lake Storage 2-го поколения**, а **формат совместим с Polybase**, можно использовать действие копирования для непосредственного вызова PolyBase, чтобы хранилище данных SQL Azure могло извлечь данные из источника. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-by-using-polybase)** .
- Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-by-using-polybase)** . Промежуточное копирование также обеспечивает лучшую пропускную способность. Он автоматически преобразует данные в формат, совместимый с Polybase, сохраняет данные в хранилище BLOB-объектов Azure, а затем вызывает Polybase для загрузки данных в хранилище данных SQL.

> [!TIP]
> Дополнительные сведения см. в разделе [Рекомендации по использованию PolyBase](#best-practices-for-using-polybase).

В разделе `polyBaseSettings` действия копирования поддерживаются следующие параметры PolyBase.

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе "Аргументы" раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2 и. т. д. | Нет                                            |
| rejectType        | Указывает, является ли параметр **rejectValue** литеральным или процентным.<br/><br/>Допустимые значения: **Значение** (по умолчанию) и **Процент**. | Нет                                            |
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … | Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault    | Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase извлекает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию).<br><br> | нет                                            |

### <a name="direct-copy-by-using-polybase"></a>Прямое копирование с помощью PolyBase

PolyBase хранилища данных SQL напрямую поддерживает хранилище BLOB-объектов Azure, Azure Data Lake Storage 1-го поколения и Azure Data Lake Storage 2-го поколения. Если ваши исходные данные соответствуют условиям, описанным в этом разделе, используйте PolyBase, чтобы выполнять копирование напрямую из исходного хранилища данных в Azure Synapse Analytics. В противном случае см. сведения в разделе [Промежуточное копирование с помощью PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Дополнительные сведения об эффективном копировании данных в хранилище данных SQL см. в записи блога [Фабрика данных Azure позволяет гораздо проще и удобнее получать аналитические выводы из данных при использовании Data Lake Store с хранилищем данных SQL](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. **Связанная служба источника** имеет следующие типы и методы проверки подлинности.

    | Поддерживаемые типы хранилища данных источника                             | Поддерживаемые типы проверки подлинности источника                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md)                | Проверка подлинности на основе ключа учетной записи, проверка подлинности по управляемому удостоверению |
    | [Хранилище Azure Data Lake Gen1](connector-azure-data-lake-store.md) | Проверка подлинности субъекта-службы                            |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | Проверка подлинности на основе ключа учетной записи, проверка подлинности по управляемому удостоверению |

    >[!IMPORTANT]
    >Если в службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать проверку подлинности по управляемому удостоверению — см. раздел [Влияние использования конечных точек службы виртуальной сети со службой хранилища Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Сведения о необходимых конфигурациях в Фабрике данных см. в разделах [Большой двоичный объект Azure — проверка подлинности по управляемому удостоверению](connector-azure-blob-storage.md#managed-identity) и [Azure Data Lake Storage 2-го поколения — проверка подлинности по управляемому удостоверению](connector-azure-data-lake-storage.md#managed-identity) соответственно.

2. **Формат исходных данных** — **Parquet**, **ORC** или **текстовый файл с разделителями** со следующими конфигурациями.

   1. Путь к папке не содержит фильтр с подстановочными знаками.
   2. Имя файла пустое или указывает на единственный файл. При указании имени файла в действии копирования можно использовать только подстановочные знаки `*` или `*.*`.
   3. Параметр `rowDelimiter` имеет **значение по умолчанию**, **\n**, **\r\n** или **\r**.
   4. В параметре `nullValue` оставляется значение по умолчанию или задается **пустая строка** (""), а в параметре `treatEmptyAsNull` оставляется значение по умолчанию или задается значение true.
   5. В параметре `encodingName` оставляется значение по умолчанию или задается значение **utf-8**.
   6. Параметры `quoteChar`, `escapeChar` и `skipLineCount` не задаются. Поддержка PolyBase пропускает строку заголовка, которую в файле определения приложения можно настроить как `firstRowAsHeader`.
   7. Параметр `compression` может иметь значение **no compression**, **GZip** или **Deflate**.

3. Если источником является папка, параметр `recursive` в действии копирования должен иметь значение true.

4. Параметры `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` и `additionalColumns` не указываются.

>[!NOTE]
>Обратите внимание, что если источником является папка, то PolyBase извлекает файлы из этой папки и всех вложенных в нее папок, но не извлекает данные из файлов, имена которых начинаются с подчеркивания (_) или точки (.) (см. [описание аргумента LOCATION здесь](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)).

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

Если исходные данные исходно не совместимы с PolyBase, включите копирование данных через промежуточный экземпляр хранилища BLOB-объектов Azure (это не может быть хранилище Azure класса Premium). В таком случае Фабрика данных Azure автоматически преобразует данные, чтобы они соответствовали требованиям к формату данных PolyBase. Затем она вызывает PolyBase для загрузки данных в хранилище данных SQL. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Подробные сведения о копировании данных через промежуточный экземпляр хранилища BLOB-объектов Azure см. в разделе [Промежуточное копирование](copy-activity-performance-features.md#staged-copy).

Чтобы использовать эту функцию, создайте [связанную службу хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#linked-service-properties), которая ссылается на учетную запись хранения Azure с промежуточным хранилищем BLOB-объектов. Затем укажите свойства `enableStaging` и `stagingSettings` для действия копирования, как показано в следующем коде.

>[!IMPORTANT]
>Если в промежуточной службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать проверку подлинности по управляемому удостоверению — см. раздел [Влияние использования конечных точек службы виртуальной сети со службой хранилища Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Изучите необходимые конфигурации в Фабрике данных, приведенные в разделе [Большой двоичный объект Azure — проверка подлинности по управляемому удостоверению](connector-azure-blob-storage.md#managed-identity).

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

В следующих разделах приведены практические рекомендации в дополнение к указанным в статье [Рекомендации по использованию Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, загружающий данные в хранилище, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли **db_owner**. Чтобы узнать, как это сделать, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Ограничение размера строки и типа данных

Загрузки PolyBase ограничены записями размером менее 1 МБ. PolyBase нельзя использовать для загрузки в типы данных VARCHR(MAX), NVARCHAR(MAX) или VARBINARY(MAX). Дополнительные сведения см. в статье [Ограничения емкости хранилища данных SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если исходные данные имеют записи размером более 1 МБ, можно попробовать вертикально разбить исходные таблицы на несколько небольших. Убедитесь, что максимальный размер каждой записи не превышает предел. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и слить вместе в Azure Synapse Analytics.

Кроме того, для данных с такими широкими столбцами можно не использовать Polybase, а загружать их с помощью файла определения приложения, отключив параметр "allow PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, присвойте более высокий класс ресурсов пользователю, который загружает данные в хранилище данных SQL через PolyBase.

#### <a name="polybase-troubleshooting"></a>Устранение неполадок c PolyBase

**Загрузка в столбец с типом данных Decimal**

Если исходные данные находятся в текстовых или других несовместимых с PolyBase хранилищах (используется промежуточное копирование и PolyBase) и в них содержится пустое значение, которое необходимо загрузить в столбец с типом данных Decimal в хранилище данных SQL, может возникнуть следующая ошибка:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Решение заключается в том, чтобы снять флажок "**Использовать тип по умолчанию**" (т. е. установить для этого параметра значение false) в разделе "Параметры PolyBase" приемника действия копирования. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" — собственная конфигурация PolyBase, которая указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase извлекает данные из текстового файла.

Свойство **`tableName` в Azure Synapse Analytics**

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

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значение NULL, входные данные большого двоичного объекта для этого столбца могут быть пустыми. Но они не могут отсутствовать во входном наборе данных. Для отсутствующих значений PolyBase будет вставлять в Azure Synapse Analytics значения NULL.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a> Загрузка данных в хранилище данных SQL Azure с помощью инструкции COPY (предварительная версия)

[Инструкция COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) в хранилище данных SQL (предварительная версия) напрямую поддерживает загрузку данных из **BLOB-объекта Azure и Azure Data Lake Storage 2-го поколения**. Если исходные данные соответствуют условиям, описанным в этом разделе, можно выбрать использование инструкции COPY в ADF для загрузки данных в хранилище данных SQL Azure. Фабрика данных Azure проверяет параметры и не выполняет действие копирования, если условия не выполнены.

>[!NOTE]
>В настоящее время Фабрика данных поддерживает копирование только из источников, совместимых с инструкцией COPY, которые приведены ниже.

При использовании инструкции COPY поддерживается следующая конфигурация.

1. **Связанная служба и формат источника** могут иметь следующие типы и методы проверки подлинности.

    | Поддерживаемые типы хранилища данных источника                             | Поддерживаемые форматы           | Поддерживаемые типы проверки подлинности источника                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md)                | [Текст с разделителями](format-delimited-text.md)             | Проверка подлинности на основе ключа учетной записи, проверка подлинности с помощью подписанного URL-адреса, проверка подлинности на основе субъекта-службы, проверка подлинности по управляемому удостоверению |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Проверка подлинности на основе ключа учетной записи, проверка подлинности с помощью подписанного URL-адреса |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Проверка подлинности на основе ключа учетной записи, проверка подлинности с помощью подписанного URL-адреса |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | [Текст с разделителями](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Проверка подлинности на основе ключа учетной записи, проверка подлинности на основе субъекта-службы, проверка подлинности по управляемому удостоверению |

    >[!IMPORTANT]
    >Если в службе хранилища Azure настроена конечная точка службы виртуальной сети, необходимо использовать проверку подлинности по управляемому удостоверению — см. раздел [Влияние использования конечных точек службы виртуальной сети со службой хранилища Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Сведения о необходимых конфигурациях в Фабрике данных см. в разделах [Большой двоичный объект Azure — проверка подлинности по управляемому удостоверению](connector-azure-blob-storage.md#managed-identity) и [Azure Data Lake Storage 2-го поколения — проверка подлинности по управляемому удостоверению](connector-azure-data-lake-storage.md#managed-identity) соответственно.

2. Параметры формата могут быть следующие.

   1. Для **Parquet**: в параметре `compression` может быть задано **без сжатия**, **Snappy** или **GZip**.
   2. Для **ORC**: в параметре `compression` может быть задано **без сжатия**, **```zlib```** или **Snappy**.
   3. Для **текста с разделителями**:
      1. В параметре `rowDelimiter` может быть явно задан **один символ** или " **\r\n**", значение по умолчанию не поддерживается.
      2. В параметре `nullValue` может быть оставлено значение по умолчанию или задана **пустая строка** ("").
      3. В параметре `encodingName` может быть оставлено значение по умолчанию или задано значение **utf-8 или utf-16**.
      4. Параметр `escapeChar` должен совпадать с параметром `quoteChar` и не быть пустым.
      5. В параметре `skipLineCount` может быть оставлено значение по умолчанию или задано значение 0.
      6. В параметре `compression` может быть задано **без сжатия** или **Snappy**.

3. Если источником является папка, параметр `recursive` в действии копирования должен иметь значение true, а в параметре `wildcardFilename` должен быть задан подстановочный знак `*`. 

4. Параметры `wildcardFolderPath`, `wildcardFilename` (отличный от `*`), `modifiedDateTimeStart`, `modifiedDateTimeEnd` и `additionalColumns` не указываются.

В разделе `allowCopyCommand` действия копирования поддерживаются следующие параметры инструкции COPY.

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Задает значения по умолчанию для каждого целевого столбца в хранилище данных SQL.  Значения по умолчанию из этого свойства переопределяют ограничение DEFAULT, заданное в хранилище данных, а столбец идентификаторов не может иметь значение по умолчанию. | нет |
| additionalOptions | Дополнительные параметры, которые будут переданы в инструкцию COPY хранилища данных SQL непосредственно в предложение "With" [инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Значение необходимо заключать в кавычки для соответствия требованиям инструкции COPY. | нет |

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

## <a name="mapping-data-flow-properties"></a>Свойства потока данных для сопоставления

При преобразовании данных в потоке данных для сопоставления можно выполнять операции чтения и записи в таблицах из Azure Synapse Analytics. Дополнительные сведения см. в описаниях [преобразования источника](data-flow-source.md) и [преобразования приемника](data-flow-sink.md) в разделе, посвященном потокам данных для сопоставления.

### <a name="source-transformation"></a>Преобразование источника

Параметры, относящиеся к Azure Synapse Analytics, находятся на вкладке **Параметры источника** преобразования источника.

**Входные данные** Укажите, следует ли указывать источник в таблице (эквивалент ```Select * from <table-name>``` ) или введите пользовательский SQL-запрос.

**Включить промежуточное хранение** Настоятельно рекомендуется использовать этот параметр в рабочих нагрузках с источниками синапсе DW. При выполнении действия потока данных с Синапасе источниками из конвейера ADF запросит учетную запись хранения промежуточного расположения и будет использовать ее для промежуточной загрузки данных. Это самый быстрый механизм загрузки данных из синапсе DW.

**Запрос.** Если в поле входных данных вы выбрали запрос, введите здесь SQL-запрос для источника. Этот параметр переопределяет любую таблицу, выбранную в наборе данных. Предложения **Order By** здесь не поддерживаются, но можно задать полную инструкцию SELECT FROM. Кроме того, можно использовать табличные функции, определяемые пользователем. Пример использования такой функции в SQL — инструкция **select * from udfGetData()** , возвращающая таблицу. Этот запрос создаст исходную таблицу, которую можно использовать в потоке данных. Использование запросов — также отличный способ сокращения количества строк для тестирования или поиска.

Пример SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Размер пакета.** Введите размер пакета для разделения больших наборов данных на блоки для чтения. В потоках данных ADF будет использовать этот параметр для установки кэширования Spark по столбцам. Это поле параметра, в котором будут использоваться параметры Spark по умолчанию, если они не заданы.

**Уровень изоляции**. Значение по умолчанию для источников SQL в потоке данных для сопоставления — Read Uncommitted (чтение незафиксированных данных). Здесь можно изменить уровень изоляции на одно из следующих значений.

- Read Committed (чтение зафиксированных данных)
- Read Uncommitted (чтение незафиксированных данных)
- Повторяющаяся операция чтения
- Serializable *-None (игнорировать уровень изоляции)

![Уровень изоляции](media/data-flow/isolationlevel.png "Уровень изоляции")

### <a name="sink-transformation"></a>Преобразование приемника

Параметры, относящиеся к Azure Synapse Analytics, находятся на вкладке **Параметры**  преобразования приемника.

**Метод обновления**. Определяет, какие операции разрешены в назначении базы данных. По умолчанию разрешены только операции вставки. Для выполнения обновления (update), обновления или вставки (upsert) или удаления (delete) строк требуется преобразование alter-row строк, отмеченных для этих действий. Для выполнения обновления (update), обновления или вставки (upsert) или удаления (delete) должен быть установлен ключевой столбец (или столбцы), позволяющий определить строки для изменения.

**Действие таблицы**. Определяет, следует ли повторно создавать или удалять все строки в целевой таблице перед записью.

- None (нет): никакие действия над таблицей не выполняются.
- Recreate (создать повторно): таблица будет удалена и создана повторно. Это действие необходимо, если новая таблица создается динамически.
- Truncate (усечь): все строки из целевой таблицы будут удалены.

**Включить промежуточный процесс**. Определяет, следует ли использовать [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) при записи в Azure Synapse Analytics

**Размер пакета.** Определяет, сколько строк записывается в каждую группу. Более крупные размеры пакетов улучшают сжатие и оптимизацию памяти, но при кэшировании данных возникает риск нехватки памяти.

**Скрипты SQL предобработки и постобработки**. Введите многострочные скрипты SQL, которые будут выполняться до (предобработка) и после (постобработка) записи данных в базу данных-приемник.

![Скрипты SQL предобработки и постобработки](media/data-flow/prepost1.png "Скрипты обработки SQL")

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Подробные сведения об этих свойствах см. в разделе [Действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия GetMetadata

Подробные сведения об этих свойствах см. в разделе [Действие GetMetadata](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Сопоставление типов данных для Azure Synapse Analytics

При копировании данных в Azure Synapse Analytics и обратно используются следующие сопоставления между типами данных Azure Synapse Analytics и промежуточными типами данных Фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

>[!TIP]
>См. раздел "Обходные решения для неподдерживаемых типов данных" в статье [Типы данных таблиц в Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md).

| Тип данных Azure Synapse Analytics    | Тип промежуточных данных фабрики данных |
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
| Изображение                                 | Byte[]                         |
| INT                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| NUMERIC                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Один                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Дата и время                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| UNIQUEIDENTIFIER                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Дальнейшие действия

В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
