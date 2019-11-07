---
title: Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS
description: В этой статье описывается включение аутентификации Azure Active Directory с использованием управляемого удостоверения для службы "Фабрика данных Azure" с целью создания среды выполнения интеграции Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 5f867126762924906aefada558a65cb68e884f6f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675668"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS

В этой статье показано, как включить проверку подлинности Azure Active Directory (Azure AD) с помощью управляемого удостоверения для фабрики данных Azure (ADF) и использовать его вместо обычных методов проверки подлинности (например, проверки подлинности SQL) в следующих целях:

- Создайте Azure-SSIS Integration Runtime (IR), который будет в свою очередь подготавливать базу данных каталога SSIS (SSISDB) на сервере базы данных SQL Azure или Управляемый экземпляр от вашего имени.

- Подключайтесь к различным ресурсам Azure при запуске пакетов служб SSIS на Azure-SSIS IR.

Дополнительные сведения об управляемом удостоверении для ADF см. в статье [управляемые идентий для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  В этом сценарии проверка подлинности Azure AD с управляемым удостоверением для ADF используется только в операциях создания и последующих запусков среды выполнения интеграции SSIS, которые в свою очередь будут подключаться к SSISDB и подключиться к нему. Для выполнения пакетов служб SSIS IR будет по-прежнему подключаться к SSISDB с помощью проверки подлинности SQL с полностью управляемыми учетными записями, созданными во время подготовки SSISDB.
>-  Если вы уже создали среду SSIS с помощью проверки подлинности SQL, вы не сможете перенастроить ее для использования проверки подлинности Azure AD через PowerShell, но это можно сделать с помощью приложения портал Azure/ADF. 

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

3.  Добавьте в группу управляемое удостоверение службы "Фабрика данных Azure". Чтобы получить идентификатор объекта управляемого идентификатора (например, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, но не использовать идентификатор приложения управляемого удостоверения для этой цели), см. статью [Управление идентий для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) .

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

5.  Выберите учетную запись пользователя Azure AD, которую необходимо сделать администратором сервера, а затем нажмите **кнопку Выбрать.**

6.  На панели команд нажмите кнопку **Сохранить**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Создание на сервере Базы данных SQL Azure автономного пользователя, представляющего группу Azure AD

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1. Запустите SSMS.

2. В диалоговом окне **Подключение к серверу** введите имя сервера базы данных SQL Azure в поле **имя сервера** .

3. В поле **authentication (проверка подлинности** ) выберите **Active Directory-Universal с поддержкой MFA** (также можно использовать другие два типа проверки подлинности Active Directory, см. раздел [Настройка и Управление аутентификацией Azure AD с помощью SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. В поле **имя пользователя** введите имя учетной записи Azure AD, заданной в качестве администратора сервера, например testuser@xxxonline.com.

5. Выберите **подключить** и завершите процесс входа.

6. В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

7. Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

8. В окне запроса введите следующую команду T-SQL и нажмите кнопку **выполнить** на панели инструментов.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   В результате выполнения команды должен быть создан автономный пользователь для представления группы.

9. Сотрите данные в окне запроса, введите следующую команду T-SQL и щелкните **Выполнить** на панели инструментов:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   После выполнения этой команды автономный пользователь сможет создать базу данных (SSISDB).

10. Если база данных SSISDB создана с использованием проверки подлинности SQL и вы хотите переключиться на использование проверки подлинности Azure AD для Azure-SSIS IR для доступа к нему, щелкните правой кнопкой мыши на базе данных **SSISDB** и выберите **создать запрос**.

11. В окне запроса введите следующую команду T-SQL и нажмите кнопку **выполнить** на панели инструментов.

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

Выполните действия, описанные в разделе [предоставление администратора Azure Active Directory для управляемый экземпляр](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Добавление управляемого удостоверения для ADF в качестве пользователя управляемого экземпляра Базы данных SQL Azure

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Запустите SSMS.

2.  Подключитесь к Управляемый экземпляр с помощью учетной записи SQL Server, которая является **sysadmin**. Это временное ограничение, которое будет удалено после того, как участники сервера Azure AD (имена входа) для Управляемый экземпляр Базы данных SQL Azure станут общедоступными. При попытке использовать учетную запись администратора Azure AD для создания имени входа вы увидите следующую ошибку: сообщение 15247, уровень 16, состояние 1, пользователь строки 1 не имеет разрешения на выполнение этого действия.

3.  В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

4.  Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

5.  В окне запроса выполните следующий скрипт T-SQL, чтобы добавить управляемое удостоверение для ADF-файла в качестве пользователя.

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF получит возможность для создания базы данных (SSISDB).

6.  Если база данных SSISDB создана с использованием проверки подлинности SQL и вы хотите переключиться на использование проверки подлинности Azure AD для Azure-SSIS IR для доступа к нему, щелкните правой кнопкой мыши на базе данных **SSISDB** и выберите **создать запрос**.

7.  В окне запроса введите следующую команду T-SQL и нажмите кнопку **выполнить** на панели инструментов.

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

2.  Не задавайте в скрипте параметр `CatalogAdminCredential`. Например:

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Запуск пакетов SSIS с проверкой подлинности с помощью управляемого удостоверения

При запуске пакетов служб SSIS на Azure-SSIS IR можно использовать проверку подлинности управляемого удостоверения для подключения к различным ресурсам Azure. Сейчас мы уже поддерживали аутентификацию управляемого удостоверения в следующих диспетчерах соединений.

- [Диспетчер подключений OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Диспетчер соединений ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Диспетчер подключений службы хранилища Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
