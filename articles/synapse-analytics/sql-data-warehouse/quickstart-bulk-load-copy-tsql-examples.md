---
title: Способы проверки подлинности с помощью инструкции COPY
description: Описание способов проверки подлинности для массовой загрузки данных
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 05dd1f1d429b59c4d621b63c6b78a1fc00e8d4dd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528469"
---
# <a name="securely-load-data-using-synapse-sql"></a>Безопасная загрузка данных с помощью Synapse SQL

В этой статье описано использование безопасных способов проверки подлинности для [инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) и приведены соответствующие примеры. Инструкция COPY — это наиболее гибкий и безопасный способ массовой загрузки данных в Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Поддерживаемые способы проверки подлинности

В следующей таблице описаны поддерживаемые методы проверки подлинности для каждого типа файла и учетной записи хранения. Они относятся к исходному месту хранения и расположению файла ошибок.

|                          |                CSV                |              Parquet              |                ORC                |
| :----------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  **Хранилище BLOB-объектов Azure**  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |              SAS/KEY              |              SAS/KEY              |
| **Azure Data Lake 2-го поколения** | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Ключ учетной записи хранения с символами LF в качестве признака конца строки (новая строка в стиле UNIX)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Символ перевода строки или новой строки указывается с помощью шестнадцатеричного значения (0x0A). Обратите внимание, что инструкция COPY интерпретирует строку "\n" как "\r\n" (возврат каретки и новая строка).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>Б. Подписанные URL-адреса (SAS) с символами CRLF в качестве признака конца строки (новая строка в стиле Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Не указывайте ROWTERMINATOR в виде "\r\n", поскольку эта строка будет интерпретирована как "\r\r\n", что может привести к ошибкам синтаксического анализа.

## <a name="c-managed-identity"></a>В. Управляемое удостоверение

Проверка подлинности с помощью управляемого удостоверения требуется, когда учетная запись хранения подключена к виртуальной сети. 

### <a name="prerequisites"></a>Предварительные требования

1. Установите Azure PowerShell, следуя инструкциям в этом [руководстве](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. При наличии учетной записи хранения общего назначения версии 1 или учетной записи хранилища BLOB-объектов необходимо сначала выполнить обновление до учетной записи хранения общего назначения версии 2, следуя инструкциям в этом [руководстве](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Необходимо включить параметр **Разрешить доверенным службам Майкрософт доступ к этой учетной записи хранения** в меню параметров **Брандмауэры и виртуальные сети** учетной записи службы хранилища Azure. Дополнительные сведения см. в [этом руководстве](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).
#### <a name="steps"></a>Шаги

1. В PowerShell **зарегистрируйте свой сервер SQL** в каталоге Azure Active Directory (AAD).

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. Создайте **учетную запись хранения общего назначения версии 2** с помощью этого [руководства](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

   > [!NOTE]
   > При наличии учетной записи хранения общего назначения версии 1 или учетной записи хранилища BLOB-объектов необходимо **сначала выполнить обновление до учетной записи хранения версии 2**, следуя инструкциям в этом [руководстве](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

3. В своей учетной записи хранения перейдите к элементу **Управление доступом (IAM)** и выберите **Добавить назначение ролей**. Назначьте своему серверу SQL одну из следующих ролей Azure: **владелец данных BLOB-объектов хранилища, участник для данных BLOB-объектов хранилища или читатель данных BLOB-объектов хранилища**.

   > [!NOTE]
   > Этот шаг могут выполнять только участники с правами владельца. Сведения о различных встроенных ролях Azure см. в этом [руководстве](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > Укажите одну из следующих ролей Azure: владелец, участник или читатель **данных BLOB-объектов** **хранилища**. Эти роли отличаются от встроенных ролей Azure владельца, участника и читателя. 

    ![Предоставление разрешения RBAC на загрузку](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Теперь можно выполнить инструкцию COPY, указав Managed Identity.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication-aad"></a>Г. Проверка подлинности Azure Active Directory (AAD)
#### <a name="steps"></a>Шаги

1. В своей учетной записи хранения перейдите к элементу **Управление доступом (IAM)** и выберите **Добавить назначение ролей**. Назначьте пользователю AAD одну из следующих ролей Azure: **владелец данных BLOB-объектов хранилища, участник для данных BLOB-объектов хранилища или читатель данных BLOB-объектов хранилища**. 

    > [!IMPORTANT]
    > Укажите одну из следующих ролей Azure: владелец, участник или читатель **данных BLOB-объектов** **хранилища**. Эти роли отличаются от встроенных ролей Azure владельца, участника и читателя.

    ![Предоставление разрешения RBAC на загрузку](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Настройте проверку подлинности Azure AD, выполнив [соответствующие инструкции](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server). 

3. Подключитесь к пулу SQL с помощью Active Directory. Теперь вы можете выполнять инструкцию COPY без указания учетных данных.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>Д. Проверка подлинности на основе субъекта-службы
#### <a name="steps"></a>Шаги

1. [Создайте приложение Azure Active Directory (AAD).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [Получите идентификатор приложения.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [Получите ключ проверки подлинности.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [Получите конечную точку маркера OAuth 2.0 версии V1.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. В учетной записи хранения [назначьте приложению AAD разрешения на чтение, запись и выполнение](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder).
6. Выполните инструкцию COPY.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Используйте версию **V1** конечной точки маркера OAuth 2.0.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с подробными сведениями о синтаксисе в статье [об инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax).
- Прочитайте обзорную статью [о загрузке данных](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt), где приведены рекомендации по загрузке.
