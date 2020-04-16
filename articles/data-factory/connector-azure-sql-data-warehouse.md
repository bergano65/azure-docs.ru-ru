---
title: Копирование и преобразование данных в Azure Synapse Analytics
description: Узнайте, как копировать данные в и из Azure Synapse Analytics и преобразовывать данные в Azure Synapse Analytics с помощью Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417578"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование и преобразование данных в Azure Synapse Analytics (ранее Хранилище данных Azure S'L) с помощью Azure Data Factory 

> [!div class="op_single_selector" title1="Выберите используемую вами версию службы Data Factory:"]
> * [Версия1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Текущая версия](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье излагается, как использовать активность копирования в фабрике данных Azure для копирования данных из и в Azure Synapse Analytics и использовать поток данных для преобразования данных в Azure Data Lake Storage Gen2. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье излагается, как использовать активность копирования в фабрике данных Azure для копирования данных из хранилища данных Azure S'L и использования потока данных для преобразования данных в Azure Data Lake Storage Gen2. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

В этой статье излагается, как использовать активность копирования в фабрике данных Azure для копирования данных из и в Azure Synapse Analytics и использовать поток данных для преобразования данных в Azure Data Lake Storage Gen2. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот разъем Azure Synapse Analytics поддерживается для следующих действий:

- [Копирование активности](copy-activity-overview.md) с [помощью поддерживаемой матричной таблицы источника/раковины](copy-activity-overview.md)
- [Картирование потока данных](concepts-data-flow-overview.md)
- [Активность поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Для деятельности Copy этот разъем Azure Synapse Analytics поддерживает следующие функции:

- копирование данных с использованием аутентификации SQL и аутентификации на основе маркеров приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемыми удостоверениями ресурсов Azure;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- Как раковина, загрузите данные с помощью [полибазы](#use-polybase-to-load-data-into-azure-sql-data-warehouse) или [COPY оператора](#use-copy-statement) (предварительный просмотр) или объемной вставки. Мы рекомендуем заявление PolyBase или COPY (предварительный просмотр) для лучшей производительности копирования.

> [!IMPORTANT]
> Если вы копируете данные с помощью Runtime интеграции Azure Data Factory, назначаем [брандмауэр сервера Azure S'L,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) чтобы службы Azure могли получить доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. Этот диапазон включает ip-адрес машины, который используется для подключения к Azure Synapse Analytics.

## <a name="get-started"></a>Начало работы

> [!TIP]
> Для достижения наилучшей производительности используйте PolyBase для загрузки данных в Azure Synapse Analytics. [Использование PolyBase для загрузки данных в раздел Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) имеет подробную информацию. Для пошагового пром. [Load 1 TB into Azure Synapse Analytics under 15 minutes with Azure Data Factory](load-azure-sql-data-warehouse.md)с примером использования см.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

В следующих разделах приводится подробная информация о свойствах, определяющих сущности фабрики данных, специфичные для разъема Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Свойства связанной службы

Следующие свойства поддерживаются для службы, связанной с Azure Synapse Analytics:

| Свойство            | Описание                                                  | Обязательно                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Свойство типа должно быть установлено на **AzureSqlDW.**             | Да                                                          |
| connectionString    | Укажите информацию, необходимую для подключения к экземпляру Azure Synapse Analytics для свойства **подключенияString.** <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить ключ субъекта-службы и пароль в Azure Key Vault и в случае аутентификации SQL получить конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да                                                          |
| servicePrincipalId  | Укажите идентификатора клиента приложения.                         | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| tenant              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia          | [Время выполнения интеграции,](concepts-integration-runtime.md) используемое для подключения к хранилику данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | нет                                                           |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности СЗЛ](#sql-authentication)
- Проверка подлинности маркеров приложения Azure AD: [основной сервис](#service-principal-authentication)
- Проверка маркеров приложений Azure AD: [Управляемые идентификаторы для ресурсов Azure](#managed-identity)

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

2. **[Предоставить администратора active Directory Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для вашего сервера Azure S'L на портале Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

3. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующий код T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполнить следующий код, или сослаться на более параметры [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать PolyBase для загрузки данных, узнайте [необходимое разрешение базы данных.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Настройте службу анализа Azure Synapse** на фабрике данных Azure.


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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Управляемые идентификаторы для проверки подлинности ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет определенную фабрику. Эту управляемую идентификацию можно использовать для проверки подлинности Azure Synapse Analytics. Назначенная фабрика может получить доступ и скопировать данные из вашего хранилища данных или в него, используя этот идентификатор.

Чтобы использовать управляемую проверку подлинности идентификации, выполните следующие действия:

1. **[Предоставить администратора active Directory Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для вашего сервера Azure S'L на портале Azure, если вы еще этого не сделали. Администратор Azure AD может быть пользователем Azure AD или группой Azure AD. Если вы предоставляете группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

2. **[Создание содержащихся пользователей баз данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для управляемой идентификации Data Factory. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Предоставить управляемой идентификации Data Factory необходимы разрешения,** как это обычно делается для пользователей ИЗл и других пользователей. Выполнить следующий код, или сослаться на более параметры [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать PolyBase для загрузки данных, узнайте [необходимое разрешение базы данных.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Настройте службу анализа Azure Synapse** на фабрике данных Azure.

**Примере:**

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

Следующие свойства поддерживаются для набора данных Azure Synapse Analytics:

| Свойство  | Описание                                                  | Обязательно                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Свойство **типа** набора данных должно быть установлено на **AzureSqlDWTable.** | Да                         |
| схема | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| table | Название таблицы/просмотра. |"Нет" для источника, "Да" для приемника  |
| tableName | Название таблицы/вид со схемой. Это свойство поддерживается для обратной совместимости. Для новой рабочей `schema` `table`нагрузки, использование и . | "Нет" для источника, "Да" для приемника |

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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе приводится список свойств, поддерживаемых источником и раковиной Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Аналитика Azure Synapse как источник

Чтобы скопировать данные из Azure Synapse Analytics, установите свойство **типа** в источнике копирования активности на **sqlDWSource.** Следующие свойства поддерживаются в **разделе источник** копирования активности:

| Свойство                     | Описание                                                  | Обязательно |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Свойство **типа** источника копирования должно быть установлено на **sqlDWSource.** | Да      |
| sqlReaderQuery               | Используйте пользовательский SQL-запрос для чтения данных. Например, `select * from MyTable`. | нет       |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | нет       |
| storedProcedureParameters    | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | нет       |
| isolationLevel | Определяет поведение блокировки транзакции для источника S'L. Разрешенные значения: **ReadCommitted** (по умолчанию), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Снимок**. Для получения более подробной информации обратитесь к [этому документу.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | нет |

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

**Процедура хранения образцов:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Аналитика Azure Synapse как раковина

Azure Data Factory поддерживает три способа загрузки данных в хранилище данных S'L.

![Параметры копирования раковины DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Использование PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Используйте заявление COPY (предварительный просмотр)](#use-copy-statement)
- Использование навало-вставки

Самый быстрый и масштабируемый способ загрузки данных — это [полибаза](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) или [заявление COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (предварительный просмотр).

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. Следующие свойства поддерживаются в разделе **раковина активности** копирования:

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | **Свойство типа** раковины копирования деятельности должно быть установлено на **SqlDWSink.** | Да                                           |
| allowPolyBase     | Указывает, следует ли использовать PolyBase для загрузки данных в хранилище данных S'L. `allowCopyCommand`и `allowPolyBase` не может быть и так, так и верно. <br/><br/>Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br/>Применить при использовании PolyBase.     |
| polyBaseSettings  | Группа свойств, которые могут быть `allowPolybase` указаны, когда свойство настроено на **истину.** | Нет.<br/>Применить при использовании PolyBase. |
| allowCopyCommand | Указывается, следует ли использовать [заявление COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (предварительный просмотр) для загрузки данных в хранилище данных S'L. `allowCopyCommand`и `allowPolyBase` не может быть и так, так и верно. <br/><br/>[Прослежись используйте выписку COPY для загрузки данных в раздел хранилища данных Azure s'L](#use-copy-statement) для ограничений и деталей.<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет.<br>Применить при использовании COPY. |
| copyCommandSettings | Группа свойств, которые могут `allowCopyCommand` быть указаны, когда свойство настроено на TRUE. | Нет.<br/>Применить при использовании COPY. |
| writeBatchSize    | Количество строк для вставок в таблицу S'L **на одну партию.**<br/><br/>Допустимое значение: **целое число** (количество строк). По умолчанию Data Factory динамически определяет соответствующий размер партии в зависимости от размера строки. | Нет.<br/>Нанесите при использовании навалом вставки.     |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не закончится срок ее действия.<br/><br/>Разрешенное значение - **это промежуток времени.** Пример: 00:30:00 (30 минут). | Нет.<br/>Нанесите при использовании навалом вставки.        |
| preCopyScript     | Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в хранилище данных SQL Azure при каждом выполнении. Это свойство используется для очистки предварительно загруженных данных. | нет                                            |
| таблицаВариант | Определяется, следует ли автоматически создавать таблицу раковины, если не существует на основе схемы источника. Автоматическое создание таблицы не поддерживается, когда поэтапная копия настроена в активности копирования. Разрешенные значения: `none` (по `autoCreate`умолчанию), . |нет |
| отключитьMetricsCollection | Фабрика данных собирает такие показатели, как DWUs склада данных S'L для оптимизации производительности копий и рекомендаций. Если вы обеспокоены этим `true` поведением, укажите, чтобы выключить его. | Нет (значение по умолчанию — `false`) |

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

Использование [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) — это эффективный способ загрузки большого объема данных в аналитику Azure Synapse с высокой пропускной способности. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Для пошагового пром. с примером использования [см.](v1/data-factory-load-sql-data-warehouse.md)

* Если исходные данные в **Azure Blob, Azure Data Lake Storage Gen1 или Azure Data Lake Storage Gen2,** а **формат совместим с PolyBase,** вы можете использовать активность копирования для прямого использования PolyBase, чтобы позволить хранилище данных Azure s'L вытащить данные из источника. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-by-using-polybase)**.
* Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-by-using-polybase)**. Промежуточное копирование также обеспечивает лучшую пропускную способность. Он автоматически преобразует данные в формат, совместимый с PolyBase, хранит данные в хранилище Azure Blob., а затем вызывает PolyBase для загрузки данных в хранилище данных S'L.

>[!TIP]
>Узнайте больше о [рекомендациях по использованию PolyBase](#best-practices-for-using-polybase).

Следующие настройки PolyBase `polyBaseSettings` поддерживаются в активности копирования:

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Подробнее о вариантах отказа от PolyBase читайте в разделе Аргументы в пользу [CREATE EXTERNAL TABLE (Transact-S'L).](https://msdn.microsoft.com/library/dn935021.aspx) <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2 и. т. д. | нет                                            |
| rejectType        | Указывает, является ли параметр **rejectValue** литеральным или процентным.<br/><br/>Разрешенные значения **значение** (по умолчанию) и **процент**. | нет                                            |
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … | Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault    | Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase извлекает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию).<br><br> | нет                                            |

### <a name="direct-copy-by-using-polybase"></a>Прямое копирование с помощью PolyBase

Хранилище данных PolyBase непосредственно поддерживает Azure Blob, Azure Data Lake Storage Gen1 и Azure Data Lake Storage Gen2. Если исходные данные соответствуют критериям, описанным в этом разделе, используйте PolyBase для копирования непосредственно из хранилища исходных данных в Azure Synapse Analytics. В противном случае см. сведения в разделе [Промежуточное копирование с помощью PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Для эффективной копирования данных на хранилище данных S'L, узнать больше из [Azure Data Factory делает его еще проще и удобнее раскрыть информацию из данных при использовании Data Lake Store с хранилищем данных S'L.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. **Служба, связанная с источником,** связана со следующими типами и методами аутентификации:

    | Поддерживаемый тип хранилища исходных данных                             | Поддерживаемый тип проверки подлинности источника                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Большой двоичный объект Azure](connector-azure-blob-storage.md)                | Проверка подлинности ключа учетной записи, управляемая аутентификация личности |
    | [Хранилище Azure Data Lake Storage 1-го поколения](connector-azure-data-lake-store.md) | Проверка подлинности субъекта-службы                            |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | Проверка подлинности ключа учетной записи, управляемая аутентификация личности |

    >[!IMPORTANT]
    >Если хранилище Azure настроено с помощью конечной точки службы VNet, необходимо использовать управляемую проверку подлинности идентификации - обратитесь к [Impact использования конечных точек службы VNet с хранилищем Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Изучите необходимые конфигурации в Data Factory от [Azure Blob - управляемой проверки подлинности и](connector-azure-blob-storage.md#managed-identity) [хранения данных Azure Data Lake Data Gen2 - управляемого раздела проверки подлинности идентификации](connector-azure-data-lake-storage.md#managed-identity) соответственно.

2. **Формат исходных данных** — **Parquet,** **ORC**или **Delimited text,** со следующими конфигурациями:

   1. Путь Folder не содержит фильтр айстабры.
   2. Имя файла пусто или указывает на один файл. Если вы укажете имя файла подстановочного знака в активности копирования, это может быть `*` только или `*.*`.
   3. `rowDelimiter`по **умолчанию,** **Ан**, **Зрон**, или **Зр**.
   4. `nullValue`остается по умолчанию или устанавливается `treatEmptyAsNull` на **пустую строку** (""), и остается по умолчанию или установлен на истину.
   5. `encodingName`остается по умолчанию или установлен **на utf-8**.
   6. `quoteChar`, `escapeChar`, `skipLineCount` и не указаны. Поддержка поддержки PolyBase пропускает строку `firstRowAsHeader` заголовка, которая может быть настроена как в ADF.
   7. `compression`может быть **никакого сжатия,** **ГЗип**, или **deflate**.

3. Если ваш источник является `recursive` папкой, в копировании действие должно быть установлено, чтобы верно.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` и не указаны.

>[!NOTE]
>Если ваш источник является папкой, обратите внимание, что PolyBase извлекает файлы из папки и всех ее субфолок, и он не извлекает данные из файлов, для которых имя файла начинается с выделения (я) или периода (.), как описано [здесь - аргумент LOCATION](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

Если исходные данные не совместимы с PolyBase, включите копирование данных через промежуточный экземпляр хранения Azure Blob (это не может быть премиум-хранилище Azure). В этом случае Azure Data Factory автоматически преобразует данные в соответствии с требованиями формата данных PolyBase. Затем он вызывает PolyBase для загрузки данных в хранилище данных S'L. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Подробные сведения о копировании данных через промежуточный экземпляр хранилища BLOB-объектов Azure см. в разделе [Промежуточное копирование](copy-activity-performance-features.md#staged-copy).

Чтобы использовать эту функцию, создайте [службу хранения Azure Blob, связанную](connector-azure-blob-storage.md#linked-service-properties) с системой хранения, которая относится к учетной записи хранения Azure с временным хранилищем blob. Затем укажите свойства `enableStaging` и `stagingSettings` свойства для копирования деятельности, как показано в следующем коде.

>[!IMPORTANT]
>Если постановочная система Azure Storage настроена с помощью конечной точки службы VNet, необходимо использовать управляемую проверку подлинности идентификации - обратитесь к [Impact использования конечных точек службы VNet с хранилищем Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Изучите необходимые конфигурации в Data Factory от [Azure Blob - управляемой проверки подлинности идентификации.](connector-azure-blob-storage.md#managed-identity)

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

Следующие разделы предоставляют лучшие практики в дополнение к тем, которые упоминаются в [рекомендациях Azure Synapse Analytics.](../synapse-analytics/sql/best-practices-sql-pool.md)

#### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, загружающий данные в хранилище, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли **db_owner**. Чтобы узнать, как это сделать, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Ограничение размера строки и типа данных

Загрузки PolyBase ограничены записями размером менее 1 МБ. Он не может быть использован для загрузки в VARCHR (MAX), NVARCHAR (MAX) или VARBINARY (MAX). Дополнительные сведения см. в статье [Ограничения емкости хранилища данных SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если исходные данные имеют записи размером более 1 МБ, можно попробовать вертикально разбить исходные таблицы на несколько небольших. Убедитесь, что максимальный размер каждой записи не превышает предел. Меньшие таблицы могут быть загружены с помощью PolyBase и объединены в Azure Synapse Analytics.

Кроме того, для данных с такими широкими столбцов можно использовать не-PolyBase для загрузки данных с помощью ADF, выключив настройки "разрешить PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, присвойте более высокий класс ресурсов пользователю, который загружает данные в хранилище данных SQL через PolyBase.

#### <a name="polybase-troubleshooting"></a>Устранение неполадок c PolyBase

**Загрузка в десятичную колонку**

Если исходные данные находятся в текстовом формате или в других неполибазаных совместимых магазинах (с использованием постановочной копии и PolyBase), и они содержат пустое значение для загрузки в десятичную колонку хранилища данных, вы можете нажать следующую ошибку:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Решение заключается в том, чтобы не выбрать опцию **"Использование типа по умолчанию"**(как ложный) в поглотителе активности копирования - > настройках PolyBase. ["USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" является нативной конфигурацией PolyBase, которая определяет, как обрабатывать недостающие значения в делимитированных текстовых файлах, когда PolyBase извлекает данные из текстового файла. 

**`tableName`в аналитике Azure Synapse**

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

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значение NULL, входные данные большого двоичного объекта для этого столбца могут быть пустыми. Но они не могут отсутствовать во входном наборе данных. PolyBase вставляет NULL для отсутствующих значений в Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Используйте выписку COPY для загрузки данных в хранилище данных Azure S'L (предварительный просмотр)

[Заявление](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (предварительный просмотр) хранилища данных (предварительный просмотр) непосредственно поддерживает данные о загрузке с **Azure Blob и Azure Data Lake Storage Gen2.** Если исходные данные соответствуют критериям, описанным в этом разделе, вы можете использовать выписку COPY в ADF для загрузки данных в хранилище данных Azure S'L. Фабрика данных Azure проверяет настройки и не выполняет действие копирования, если критерии не выполнены.

>[!NOTE]
>В настоящее время Data Factory только копия поддержки из copy оператора КОП совместимых источников, упомянутых ниже.

Использование оператора COPY поддерживает следующую конфигурацию:

1. **Служба и формат, связанный с источником,** относятся к следующим типам и методам аутентификации:

    | Поддерживаемый тип хранилища исходных данных                             | Поддерживаемый формат           | Поддерживаемый тип проверки подлинности источника                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Большой двоичный объект Azure](connector-azure-blob-storage.md)                | [Делимитированый текст](format-delimited-text.md)             | Проверка подлинности ключа учетной записи, общая аутентификация подписи доступа, основная аутентификация службы, управляемая аутентификация идентификации |
    | &nbsp;                                                       | [Паркетным](format-parquet.md)                    | Проверка подлинности ключа учетной записи, общая аутентификация подписи доступа |
    | &nbsp;                                                       | [Орк](format-orc.md)                        | Проверка подлинности ключа учетной записи, общая аутентификация подписи доступа |
    | [Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | [Делимитированый текст](format-delimited-text.md)<br/>[Паркетным](format-parquet.md)<br/>[Орк](format-orc.md) | Проверка подлинности ключа учетной записи, основная аутентификация службы, управляемая аутентификация личности |

    >[!IMPORTANT]
    >Если хранилище Azure настроено с помощью конечной точки службы VNet, необходимо использовать управляемую проверку подлинности идентификации - обратитесь к [Impact использования конечных точек службы VNet с хранилищем Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Изучите необходимые конфигурации в Data Factory от [Azure Blob - управляемой проверки подлинности и](connector-azure-blob-storage.md#managed-identity) [хранения данных Azure Data Lake Data Gen2 - управляемого раздела проверки подлинности идентификации](connector-azure-data-lake-storage.md#managed-identity) соответственно.

2. Настройки формата приведены в следующем:

   1. Для **паркета**: `compression` не может быть **сжатия**, **Snappy**, или **ГЗип**.
   2. Для **ORC** `compression` : не может быть **```zlib```** **сжатия,**, или **Snappy**.
   3. Для **ограниченного текста:**
      1. `rowDelimiter`четко устанавливается как **один символ** или «**Зрон»,** значение по умолчанию не поддерживается.
      2. `nullValue`остается по умолчанию или устанавливается на **пустую строку** ("").
      3. `encodingName`остается по умолчанию или установлен **на utf-8 или utf-16.**
      4. `escapeChar`должны быть `quoteChar`такими же, как, и не пуст.
      5. `skipLineCount`остается по умолчанию или установлен на 0.
      6. `compression`может быть **никакого сжатия** или **ГЗип**.

3. Если ваш источник является `recursive` папкой, в копировании действие должно быть установлено, чтобы верно.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` и не указаны.

Следующие настройки заявления COPY `allowCopyCommand` поддерживаются в активности копирования:

| Свойство          | Описание                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| значения по умолчанию | Определяет значения по умолчанию для каждого целевого столбца в S'L DW.  Значения по умолчанию в свойстве перезаписи ограничения DEFAULT, установленного в хранилище данных, и столбец идентификации не может иметь значение по умолчанию. | нет |
| дополнительныеВарианты | Дополнительные опции, которые будут переданы в заявление S'L DW COPY непосредственно в пункте "С" в [заявлении COPY.](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) Цитата значение, необходимое для согласования с требованиями выписки COPY. | нет |

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


## <a name="lookup-activity-properties"></a>Свойства активности поиска

Чтобы узнать подробности о свойствах, проверьте [активность поиска.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Свойства активности GetMetadata

Чтобы узнать подробности о свойствах, проверьте [активность GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов данных для хранилища данных SQL Azure

## <a name="mapping-data-flow-properties"></a>Отображение свойств потока данных

При преобразовании данных в картографируем поток данных можно читать и записывать в таблицы из Azure Synapse Analytics. Для получения дополнительной [source transformation](data-flow-source.md) информации [sink transformation](data-flow-sink.md) см.

### <a name="source-transformation"></a>Преобразование источника

Настройки, характерные для Azure Synapse Analytics, доступны во вкладке **«Параметры исхода»** преобразования источника. 

**Вход:** Выберите, указываете ли вы свой ```Select * from <table-name>```источник за столом (эквивалент) или вводите пользовательский запрос S'L.

**Запрос**: Если вы выберете запрос в поле ввода, введите запрос для вашего источника. Эта настройка перекрывает любую таблицу, выбранную в наборе данных. **Заказ предложения** не поддерживаются здесь, но вы можете установить полное заявление SELECT FROM. Можно также использовать функции таблицы, определяемые пользователем. **select - от udfGetData()** — это UDF в S'L, который возвращает таблицу. Этот запрос создашн таблицу исходных данных, которую можно использовать в потоке данных. Использование запросов также является отличным способом уменьшения строк для тестирования или поиска. 

* Пример СЗЛ:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Размер пакета**: Введите размер пакета, чтобы куски больших данных в считывания. В потоках данных ADF будет использовать этот параметр для установки кэширования столбцов Spark. Это поле опций, которое будет использовать по умолчанию Spark, если оно оставлено пустым.

**Уровень изоляции**: По умолчанию для источников S'L при картировании потока данных читается незафиксированным. Здесь можно изменить уровень изоляции на одно из следующих значений:
* Фиксированное чтение
* Незафиксированное чтение
* Повторяющаяся операция чтения
* Упорядочиваемый уровень изоляции
* Нет (игнорировать уровень изоляции)

![Уровень изоляции](media/data-flow/isolationlevel.png "Уровень изоляции")

### <a name="sink-transformation"></a>Преобразование раковины

Настройки, характерные для аналитики Azure Synapse, доступны во вкладке **«Настройки преобразования раковины».**

**Метод обновления:** Определяет, какие операции разрешены в пункте назначения базы данных. По умолчанию разрешаются только вставки. Для обновления, обновления или удаления строк требуется преобразование строки alter-row для тегов строк для этих действий. Для обновления, upserts и удаляет, ключевые столбцы или столбцы должны быть установлены, чтобы определить, какой ряд изменить.

**Таблица действий:** Определяет, следует ли воссоздать или удалить все строки из таблицы назначения до написания.
* Нет: никакие действия не будут сделаны к столу.
* Воссоздайте: таблица будет удалена и воссоздана. Требуется при динамическом создании новой таблицы.
* Truncate: Все строки из целевой таблицы будут удалены.

**Включить постановку:** Определяет, следует ли использовать [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) при записи в Azure Synapse Analytics

**Размер пакета**: Контролирует, сколько строк пишется в каждом ведре. Большие размеры пакетов улучшают сжатие и оптимизацию памяти, но рискуют из-за исключений памяти при кэшировать данные.

**До и после S'L скрипты**: Введите многолинейные скрипты S'L, которые будут выполняться до (предварительная обработка) и после (после обработки) данные записываются в базу данных Sink

![до и после s-L обработки скриптов](media/data-flow/prepost1.png "Скрипты обработки S'L")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Отображение типов данных для аналитики Azure Synapse

При копировании данных из или в Azure Synapse Analytics следующие отображения используются с типов данных Azure Synapse Analytics в промежуточные типы данных Azure Data Factory. Просмотрите [схемы и отображения типов данных,](copy-activity-schema-and-type-mapping.md) чтобы узнать, как Copy Activity отображает схему исхода и тип данных в раковину.

>[!TIP]
>Ссылайтесь на [типы данных таблицы в статье Azure Synapse Analytics,](../synapse-analytics/sql/develop-tables-data-types.md) на которых поддерживаемые типы данных и обходные пути для неподдерживаемых.

| Тип данных Azure Synapse Analytics    | Тип промежуточных данных фабрики данных |
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
Для списка хранилищ данных, поддерживаемых в качестве источников и поглотителей в рамках Copy Activity в Azure Data Factory, [см.](copy-activity-overview.md#supported-data-stores-and-formats)
