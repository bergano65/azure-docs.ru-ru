---
title: Enable Azure Active Directory for Azure-SSIS Integration Runtime
description: В этой статье описывается включение аутентификации Azure Active Directory с использованием управляемого удостоверения для службы "Фабрика данных Azure" с целью создания среды выполнения интеграции Azure-SSIS.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217699"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS

This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Create an Azure-SSIS Integration Runtime (IR) that will in turn provision SSIS catalog database (SSISDB) in Azure SQL Database server/Managed Instance on your behalf.

- Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

For more info about the managed identity for your ADF, see [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In this scenario, Azure AD authentication with the managed identity for your ADF is only used in the creation and subsequent starting operations of your SSIS IR that will in turn provision and connect to SSISDB. For SSIS package executions, your SSIS IR will still connect to SSISDB using SQL authentication with fully managed accounts that are created during SSISDB provisioning.
>-  If you have already created your SSIS IR using SQL authentication, you can not reconfigure it to use Azure AD authentication via PowerShell at this time, but you can do so via Azure portal/ADF app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Включение Azure AD в службе "База данных SQL Azure"

Сервер Базы данных SQL Azure поддерживает создание базы данных с использованием пользователя Azure AD. Для начала нужно создать группу Azure AD с управляемым удостоверением, членом которой станет ADF. Далее необходимо назначить одного из пользователей Azure AD администратором Active Directory для сервера Базы данных SQL Azure, а затем подключиться к этому серверу через SQL Server Management Studio (SSMS) под учетными данными этого пользователя. И в конечном итоге потребуется создать автономного пользователя, представляющего группу Azure AD, чтобы позволить Azure-SSIS IR использовать управляемое удостоверение для ADF с целью создания SSISDB от вашего имени.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Создание группы Azure AD с управляемым удостоверением, членом которой станет ADF

Можно использовать существующую группу Azure AD или создать новую с помощью Azure AD PowerShell.

1.  Установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Войдите с помощью командлета  `Connect-AzureAD` и выполните следующий командлет, чтобы создать группу и сохранить ее в переменной:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Результат будет выглядеть как в следующем примере и содержать значение переменной.

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Добавьте в группу управляемое удостоверение службы "Фабрика данных Azure". You can follow the article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) to get the principal Managed Identity Object ID (e.g. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, but do not use Managed Identity Application ID for this purpose).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Членство в группе можно также проверить позже.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Настройка аутентификации Azure AD для сервера Базы данных SQL Azure

Для  [настройки и администрирования аутентификации Azure AD с использованием SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) выполните следующие действия.

1.  На портале Azure выберите **Все службы**  ->  **Серверы SQL Server** на левой панели навигации.

2.  Выберите сервер Базы данных SQL Azure, для которого нужно настроить аутентификацию Azure AD.

3.  В разделе колонки **Параметры** выберите **Администратор Active Directory**.

4.  На панели команд щелкните **Задать администратора**.

5.  Select an Azure AD user account to be made administrator of the server, and then select **Select.**

6.  На панели команд нажмите кнопку **Сохранить**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Создание на сервере Базы данных SQL Azure автономного пользователя, представляющего группу Azure AD

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1. Запустите SSMS.

2. In the **Connect to Server** dialog, enter your Azure SQL Database server name in the **Server name** field.

3. In the **Authentication** field, select **Active Directory - Universal with MFA support** (you can also use the other two Active Directory authentication types, see [Configure and manage Azure AD authentication with SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. In the **User name** field, enter the name of Azure AD account that you set as the server administrator, e.g. testuser@xxxonline.com.

5. select **Connect** and complete the sign-in process.

6. В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

7. Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

8. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   В результате выполнения команды должен быть создан автономный пользователь для представления группы.

9. Сотрите данные в окне запроса, введите следующую команду T-SQL и щелкните **Выполнить** на панели инструментов:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   После выполнения этой команды автономный пользователь сможет создать базу данных (SSISDB).

10. If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

11. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    В результате выполнения команды должен быть создан автономный пользователь для представления группы.

12. Сотрите данные в окне запроса, введите следующую команду T-SQL и щелкните **Выполнить** на панели инструментов:

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    В результате выполнения команды автономный пользователь получает права на доступ к SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Включение Azure AD в службе "Управляемый экземпляр Базы данных SQL Azure"

Управляемый экземпляр Базы данных SQL Azure поддерживает создание базы данных с использованием управляемого удостоверения для ADF напрямую. Присоединять управляемое удостоверение для ADF к группе Azure AD или создавать автономного пользователя, представляющего эту группу в управляемом экземпляре, не требуется.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Настройка аутентификации Azure AD для управляемого экземпляра Базы данных SQL Azure

Follow the steps in [Provision an Azure Active Directory administrator for your Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Добавление управляемого удостоверения для ADF в качестве пользователя управляемого экземпляра Базы данных SQL Azure

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Запустите SSMS.

2.  Connect to your Managed Instance using a SQL Server account that is a **sysadmin**. This is a temporary limitation that will be removed once Azure AD server principals (logins) for Azure SQL Database Managed Instance becomes GA. You will see the following error if you try to use an Azure AD admin account to create the login: Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.

3.  В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

4.  Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

5.  In the query window, execute the following T-SQL script to add the managed identity for your ADF as a user

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF получит возможность для создания базы данных (SSISDB).

6.  If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

7.  In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    После выполнения этой команды управляемое удостоверение для ADF получит возможность для доступа к SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Подготовка Azure SSIS IR на портале Azure или в приложении ADF

При подготовке Azure-SSIS IR на портале Azure или в приложении ADF на странице **Параметры SQL** установите флажок **Использовать аутентификацию AAD с управляемым удостоверением для ADF**. На приведенном ниже снимке экрана показаны параметры для среды выполнения интеграции, в которой SSISDB находится на сервере Базы данных SQL Azure. Для среды выполнения интеграции, в которой SSISDB находится в управляемом экземпляре, параметры **Уровень службы базы данных каталога** и **Разрешить службам Azure доступ** не применяются, а остальные параметры будут такими же.

Дополнительные сведения о создании Azure-SSIS IR см. в статье [Создание среды выполнения интеграции Azure-SSIS в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Параметры среды выполнения интеграции Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Подготовка Azure-SSIS IR с помощью PowerShell

Чтобы подготовить к работе среду выполнения интеграции Azure-SSIS с помощью PowerShell, сделайте следующее:

1.  Установите модуль [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Не задавайте в скрипте параметр `CatalogAdminCredential`. Пример.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Run SSIS Packages with Managed Identity Authentication

When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Currently we have already supported managed identity authentication in the following connection managers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
