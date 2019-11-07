---
title: Копирование данных в хранилище данных SQL Azure и из него с помощью фабрики данных Azure
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ в хранилище данных SQL Azure или из него в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: b64bfd046a42a630e7913c45213053e84377a037
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681145"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure 
> [!div class="op_single_selector" title1="Выберите версию службы фабрики данных, которую вы используете:"]
> * [Версии 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Текущая версия](connector-azure-sql-data-warehouse.md)

В этой статье описано, как копировать данные в хранилище данных SQL Azure и из него. Дополнительные сведения о Фабрике данных Azure см. во [вводной статье](introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Этот соединитель хранилища данных SQL Azure поддерживается для следующих действий:

- [Действие копирования](copy-activity-overview.md) с [поддерживаемой таблицей источника или приемника](copy-activity-overview.md)
- [Поток данных сопоставления](concepts-data-flow-overview.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)

Этот соединитель хранилища данных SQL Azure поддерживает такие функции:

- копирование данных с использованием аутентификации SQL и аутентификации на основе маркеров приложения Azure Active Directory (Azure AD) с субъектом-службой или управляемыми удостоверениями ресурсов Azure;
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- загрузку данных с помощью технологии PolyBase или массовой вставки (в качестве приемника). Для лучшей производительности копирования рекомендуется использовать технологию PolyBase.

> [!IMPORTANT]
> При копировании данных с использованием среды выполнения интеграции фабрики данных Azure настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он разрешал службам Azure получать доступ к серверу.
> При копировании данных с помощью локальной среды выполнения интеграции настройте брандмауэр Azure SQL Server, чтобы разрешить соответствующий диапазон IP-адресов. В этот диапазон входит IP-адрес компьютера, который используется для подключения к базе данных SQL Azure.

## <a name="get-started"></a>Приступая к работе

> [!TIP]
> Для обеспечения лучшей производительности при загрузке данных в хранилище данных SQL Azure используйте PolyBase. Дополнительные сведения см. в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах для определения сущностей фабрики данных, относящихся к соединителю хранилища данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы хранилища данных SQL Azure поддерживаются следующие свойства:

| Свойство            | Description (Описание)                                                  | Обязательно                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Для свойства type необходимо задать значение **AzureSqlDW**.             | Да                                                          |
| connectionString    | Укажите сведения, необходимые для подключения к экземпляру хранилища данных SQL Azure, для свойства **connectionString**. <br/>Пометьте это поле как SecureString, чтобы безопасно хранить его в Фабрике данных. Вы также можете поместить ключ субъекта-службы и пароль в Azure Key Vault и в случае аутентификации SQL получить конфигурацию `password` из строки подключения. Ознакомьтесь с примером JSON под таблицей и с подробными сведениями в статье [Хранение учетных данных в Azure Key Vault](store-credentials-in-key-vault.md). | Да                                                          |
| servicePrincipalId  | Укажите идентификатора клиента приложения.                         | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| клиенте              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности Azure AD на основе субъекта-службы. |
| connectVia          | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет                                                           |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности.

- [Проверка подлинности SQL](#sql-authentication).
- Аутентификация по маркеру приложения Azure AD на основе [субъекта-службы](#service-principal-authentication).
- Аутентификация на основе маркеров приложения Azure AD: [Управляемые удостоверения для ресурсов Azure](#managed-identity)

>[!TIP]
>Если вы получили ошибку с кодом ошибки UserErrorFailedToConnectToSqlServer и сообщение типа "Предел сеанса для базы данных — XXX, и он был достигнут", добавьте `Pooling=false` в строку подключения и повторите попытку.

### <a name="sql-authentication"></a>Аутентификация SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Пример использования проверки подлинности SQL в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

1. **[Создайте приложение Azure Active Directory на портале Azure](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** . Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратором Azure AD может быть пользователь Azure AD или группа Azure AD. Если предоставить группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

3. **[Создайте пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для субъекта-службы. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL или другим пользователям. Выполните следующий код или ознакомьтесь с дополнительными параметрами [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать Polybase для загрузки данных, ознакомьтесь с [разрешениями требуемой базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Настройте в ADF связанную службу хранилища данных SQL Azure** в фабрике данных Azure.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Пример использования аутентификации на основе субъекта-службы в связанной службе

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identity"></a> Аутентификация управляемых удостоверений для ресурсов Azure

Фабрика данных может быть связана с [управляемым удостоверением ресурсов Azure](data-factory-service-identity.md), которое представляет определенную фабрику. Это управляемое удостоверение можно использовать для проверки подлинности хранилища данных SQL Azure. Назначенная фабрика может получить доступ и скопировать данные из вашего хранилища данных или в него, используя этот идентификатор.

Чтобы использовать аутентификацию управляемого удостоверения, выполните следующие действия.

1. **[Подготовьте администратора Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратором Azure AD может быть пользователь Azure AD или группа Azure AD. Если предоставить группе с управляемым удостоверением роль администратора, пропустите шаги 3 и 4. Администратор будет иметь полный доступ к базе данных.

2. **[Создание пользователей автономной базы данных](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** для управляемого удостоверения фабрики данных. Подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные с помощью таких средств, как среда SSMS, используя удостоверение Azure AD, у которого есть хотя бы разрешение ALTER ANY USER. Выполните следующую инструкцию T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Предоставьте управляемому удостоверению фабрики данных необходимые разрешения** , как обычно для пользователей SQL и других. Выполните следующий код или ознакомьтесь с дополнительными параметрами [здесь](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Если вы хотите использовать Polybase для загрузки данных, ознакомьтесь с [разрешениями требуемой базы данных](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Настройте в ADF связанную службу хранилища данных SQL Azure** в фабрике данных Azure.

**Пример**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). Этот раздел содержит список свойств, поддерживаемых набором данных хранилища данных SQL Azure.

Для копирования данных из хранилища данных SQL Azure или в него поддерживаются следующие свойства:

| Свойство  | Description (Описание)                                                  | Обязательно                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Свойство **type** для набора данных должно иметь значение: **AzureSqlDWTable**. | Да                         |
| schema | Имя схемы. |"Нет" для источника, "Да" для приемника  |
| таблица | Имя таблицы или представления. |"Нет" для источника, "Да" для приемника  |
| tableName | Имя таблицы или представления со схемой. Это свойство поддерживается для обеспечения обратной совместимости. Для новой рабочей нагрузки используйте `schema` и `table`. | "Нет" для источника, "Да" для приемника |

#### <a name="dataset-properties-example"></a>Пример свойств набора данных

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
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

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником хранилища данных SQL Azure.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Хранилище данных SQL Azure в качестве источника

Чтобы скопировать данные из хранилища данных SQL Azure, задайте для свойства **type** источника действия копирования значение **SqlDWSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство                     | Description (Описание)                                                  | Обязательно |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Свойство **type** источника действия копирования должно иметь значение **SqlDWSource**. | Да      |
| sqlReaderQuery               | Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. | Нет       |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. | Нет       |
| storedProcedureParameters    | Параметры для хранимой процедуры.<br/>Допустимые значения: пары имен или значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет       |

### <a name="points-to-note"></a>Примечания

- Если для **SqlSource** указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных. Кроме того, можно создать хранимую процедуру. Укажите параметр **sqlReaderStoredProcedureName** и **storedProcedureParameters**, если хранимая процедура принимает параметры.
- Если не указать **sqlReaderQuery** или **sqlReaderStoredProcedureName**, то столбцы, определенные в разделе **structure** набора данных JSON, будут использоваться для построения запроса. `select column1, column2 from mytable` выполняется по отношению к хранилищу данных SQL Azure. Если в определении набора данных нет раздела **structure**, выбираются все столбцы из таблицы.

#### <a name="sql-query-example"></a>Пример SQL-запроса

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

#### <a name="stored-procedure-example"></a>Пример хранимой процедуры

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Хранилище данных SQL Azure в качестве приемника

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство          | Description (Описание)                                                  | Обязательно                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Свойство **type** приемника действия копирования должно иметь значение **SqlDWSink**. | Да                                           |
| allowPolyBase     | Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT. <br/><br/> Мы рекомендуем загружать данные в хранилище данных SQL с использованием PolyBase. Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию). | Нет                                            |
| polyBaseSettings  | Группа свойств, которые можно задать, если свойство **allowPolybase** имеет значение **true**. | Нет                                            |
| rejectValue       | Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе "Аргументы" раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2 и. т. д. | Нет                                            |
| rejectType        | Указывает, является ли параметр **rejectValue** литеральным или процентным.<br/><br/>Допустимые значения: **Значение** (по умолчанию) и **Процент**. | Нет                                            |
| rejectSampleValue | Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … | Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault    | Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase получает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true** и **false** (по умолчанию).<br><br> | Нет                                            |
| writeBatchSize    | Число строк для вставки в таблицу SQL **для каждого пакета**. Применимо, только если не используется PolyBase.<br/><br/>Допустимое значение: **целое число** (количество строк). По умолчанию фабрика данных динамически определяет соответствующий размер пакета в зависимости от размера строки. | Нет                                            |
| writeBatchTimeout | Время ожидания завершения операции пакетной вставки до истечения времени ожидания. Применяется только в том случае, если Polybase не используется.<br/><br/>Допустимое значение — **timespan**. Пример: 00:30:00 (30 минут). | Нет                                            |
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

В следующем разделе можно узнать, как использовать PolyBase для эффективной загрузки в хранилище данных SQL.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.

Применение [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) — это эффективный способ загрузки большого объема данных в хранилище данных SQL Azure с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных менее чем за 15 минут](v1/data-factory-load-sql-data-warehouse.md).

* Если исходные данные находятся в **большом двоичном объекте Azure, Azure Data Lake Storage 1-го поколения или Azure Data Lake Storage 2-го поколения**, а **Формат является совместимым с polybase**, вы можете использовать действие копирования для непосредственного вызова Polybase, чтобы хранилище данных SQL Azure мог получать данные из источника. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-by-using-polybase)** .
* Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-by-using-polybase)** . Промежуточное копирование также обеспечивает лучшую пропускную способность. Оно автоматически преобразует данные в формат, совместимый с PolyBase. И он хранит данные в хранилище BLOB-объектов Azure. После этого данные загружаются в хранилище данных SQL.

>[!TIP]
>Дополнительные сведения см. в статье рекомендации [по использованию polybase](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Прямое копирование с помощью PolyBase

Polybase хранилища данных SQL напрямую поддерживает большой двоичный объект Azure, Azure Data Lake Storage 1-го поколения и Azure Data Lake Storage 2-го поколения. Если исходные данные соответствуют критериям, описанным в этом разделе, используйте Polybase для копирования непосредственно из исходного хранилища данных в хранилище данных SQL Azure. В противном случае см. сведения в разделе [Промежуточное копирование с помощью PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Чтобы эффективно копировать данные в хранилище данных SQL, Узнайте больше от [фабрики данных Azure, что упрощает и упрощает анализ данных при использовании Data Lake Store с хранилищем данных SQL](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. **Связанная служба источника** имеет следующие типы и методы проверки подлинности:

    | Поддерживаемый тип исходного хранилища данных                             | Поддерживаемый тип проверки подлинности источника                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Хранилище BLOB-объектов Azure](connector-azure-blob-storage.md)                | Проверка подлинности ключа учетной записи, управляемая идентификация |
    | [Хранилище Azure Data Lake Gen1](connector-azure-data-lake-store.md) | Проверка подлинности субъекта-службы                            |
    | [Хранилище Azure Data Lake Storage 2-го поколения](connector-azure-data-lake-storage.md) | Проверка подлинности ключа учетной записи, управляемая идентификация |

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
                    "type": "AzureBlobStorageReadSetting",
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

Если исходные данные не соответствуют критериям предыдущего раздела, включите копирование данных через промежуточный экземпляр хранилища BLOB-объектов Azure. Это не может быть хранилище Azure класса Premium. В этом случае фабрика данных Azure автоматически запускает преобразования данных для соответствия требованиям формата данных PolyBase. Затем она загружает данные в хранилище данных SQL с помощью PolyBase. Наконец, она очищает ваши временные данные из хранилища BLOB-объектов. Подробные сведения о копировании данных через промежуточный экземпляр хранилища BLOB-объектов Azure см. в разделе [Промежуточное копирование](copy-activity-performance.md#staged-copy).

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

## <a name="best-practices-for-using-polybase"></a>Советы и рекомендации по использованию PolyBase

В следующих разделах приведены практические рекомендации в дополнение к указанным в статье [Рекомендации по использованию хранилища данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, загружающий данные в хранилище, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли **db_owner**. Чтобы узнать, как это сделать, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Ограничение размера строки и типа данных

Загрузки PolyBase ограничены записями размером менее 1 МБ. Его нельзя использовать для загрузки в ПЕРЕМЕННЫЕ VARCHR (MAX), NVARCHAR (MAX) или VARBINARY (MAX). Дополнительные сведения см. в статье [Ограничения емкости хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если исходные данные имеют записи размером более 1 МБ, можно попробовать вертикально разбить исходные таблицы на несколько небольших. Убедитесь, что максимальный размер каждой записи не превышает предел. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и объединить в хранилище данных SQL Azure.

Кроме того, для данных с такими широкими столбцами можно использовать не Polybase для загрузки данных с помощью ADF, отключив параметр Allow Polybase.

### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, присвойте более высокий класс ресурсов пользователю, который загружает данные в хранилище данных SQL через PolyBase.

### <a name="polybase-troubleshooting"></a>Устранение неполадок Polybase

**Загрузка в десятичный столбец**

Если исходные данные находятся в текстовом формате или других совместимых с Polybase хранилищах (с использованием промежуточного копирования и Polybase) и содержат пустое значение для загрузки в столбец в хранилище данных SQL, может появиться следующее сообщение об ошибке:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Решение состоит в том, чтобы отменить выбор параметра "**использовать тип по умолчанию**" (false) в приемнике действия копирования — > параметры polybase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" — это собственная конфигурация polybase, которая указывает, как следует выполнять обработку отсутствующих значений в текстовых файлах с разделителями, когда polybase извлекает данные из текстового файла. 

**`tableName` в хранилище данных SQL Azure**

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

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значение NULL, входные данные большого двоичного объекта для этого столбца могут быть пустыми. Но они не могут отсутствовать во входном наборе данных. Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.

## <a name="mapping-data-flow-properties"></a>Сопоставление свойств потока данных

Дополнительные сведения см. в статье преобразование [источника](data-flow-source.md) и [Преобразование приемника](data-flow-sink.md) в потоке данных сопоставления.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов данных для хранилища данных SQL Azure

При копировании данных в хранилище данных SQL Azure или из него используются следующие сопоставления между типами данных хранилища данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

>[!TIP]
>См. статью [табличные типы данных в хранилище данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) в разделе Поддерживаемые типы данных хранилища SQL и обходные пути для неподдерживаемых.

| Тип данных хранилища данных SQL Azure    | Тип промежуточных данных фабрики данных |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary;                                | Byte[]                         |
| bit                                   | Логический                        |
| char;                                  | String, Char[]                 |
| дата                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | Datetimeoffset                 |
| DECIMAL                               | DECIMAL                        |
| Атрибут FILESTREAM (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| изображение                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | DECIMAL                        |
| nchar;                                 | String, Char[]                 |
| numeric                               | DECIMAL                        |
| nvarchar                              | String, Char[]                 |
| real;                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime;                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | DECIMAL                        |
| time                                  | TimeSpan                       |
| tinyint;                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary;                             | Byte[]                         |
| varchar.                               | String, Char[]                 |

## <a name="lookup-activity-properties"></a>Свойства действия поиска

Чтобы получить сведения о свойствах, проверьте [действие поиска](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Свойства действия с метаданными

Дополнительные сведения о свойствах см. в статье [действие с операциями](control-flow-get-metadata-activity.md) с помощью метаданных. 

## <a name="next-steps"></a>Дальнейшие действия
В таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
