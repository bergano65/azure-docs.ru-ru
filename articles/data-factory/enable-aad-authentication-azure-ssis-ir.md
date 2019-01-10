---
title: Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS | Документация Майкрософт
description: В этой статье описывается включение аутентификации Azure Active Directory с использованием управляемого удостоверения для службы "Фабрика данных Azure" с целью создания среды выполнения интеграции Azure-SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: douglasl
ms.openlocfilehash: be14eb59cb89676b0d69b94246f35ad6dfc7eed9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792653"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS

В этой статье демонстрируется, как включить аутентификацию Azure Active Directory (Azure AD) с использованием управляемого удостоверения для службы "Фабрика данных Azure" (ADF) и использовать ее вместо аутентификации SQL, чтобы создать среду выполнения интеграции Azure-SSIS (Azure-SSIS IR), которая, в свою очередь, создаст базу данных каталогов SSIS (SSISDB) на сервере или в управляемом экземпляре базы данных SQL Azure от вашего имени.

Дополнительные сведения об управляемом удостоверении службы "Фабрика данных Azure" см. в [этой статье](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Если вы уже создали Azure-SSIS IR с использованием аутентификации SQL, вы не сможете перенастроить ее на аутентификацию Azure AD с использованием PowerShell сейчас, но сможете сделать это на портале Azure или в ADF. 

## <a name="enable-azure-ad-on-azure-sql-database"></a>Включение Azure AD в службе "База данных SQL Azure"

Сервер базы данных SQL Azure поддерживает создание базы данных с использованием пользователя Azure AD. Для начала нужно создать группу Azure AD с управляемым удостоверением, членом которой станет ADF. Далее необходимо назначить одного из пользователей Azure AD администратором Active Directory для сервера базы данных SQL Azure, а затем подключиться к этому серверу через SQL Server Management Studio (SSMS) под учетными данными этого пользователя. И в конечном итоге потребуется создать автономного пользователя, представляющего группу Azure AD, чтобы позволить Azure-SSIS IR использовать управляемое удостоверение для ADF с целью создания SSISDB от вашего имени.

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

3.  Добавьте в группу управляемое удостоверение службы "Фабрика данных Azure". Чтобы получить ИДЕНТИФИКАТОР УДОСТОВЕРЕНИЯ СЛУЖБЫ субъекта (например, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc), выполните инструкции, приведенные в статье [Удостоверение службы фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity). Не используйте для этой цели идентификатор приложения удостоверения службы.

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Членство в группе можно также проверить позже.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Настройка аутентификации Azure AD для сервера базы данных SQL Azure

Для  [настройки и администрирования аутентификации Azure AD с использованием SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) выполните следующие действия.

1.  На портале Azure выберите  **Все службы**  ->  **Серверы SQL Server**  на панели навигации слева.

2.  Выберите сервер базы данных SQL Azure, для которого нужно настроить аутентификацию Azure AD.

3.  В разделе  **Параметры**  этой колонки выберите  **Администратор Active Directory**.

4.  На панели команд щелкните  **Задать администратора**.

5.  Выберите учетную запись пользователя Azure AD, которой необходимо предоставить права администратора сервера, и щелкните  **Выбрать**.

6.  На панели команд щелкните  **Сохранить**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Создание на сервере базы данных SQL Azure автономного пользователя, представляющего группу Azure AD

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Запустите SSMS.

2.  В диалоговом окне  **Подключение к серверу**  введите имя сервера базы данных SQL Azure в поле  **Имя сервера** .

3.  В поле  **Аутентификация**  выберите параметр  **Active Directory — универсальный с поддержкой MFA** (можно также использовать два других типа аутентификации Active Directory, см. статью [Настройка и администрирование аутентификации Azure Active Directory с помощью SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4.  В поле  **Имя пользователя**  введите имя учетной записи Azure AD, установленной в качестве администратора сервера, например testuser@xxxonline.com.

5.  Выберите  **Подключить** и завершите процесс входа в систему.

6.  В  **обозревателе объектов** разверните папку  **Базы данных**  ->  **Системные базы данных**.

7.  Щелкните правой кнопкой мыши базу данных **master** и выберите  **Создать запрос**.

8.  В окне запроса введите следующую команду T-SQL и щелкните  **Выполнить**  на панели инструментов:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    В результате выполнения команды должен быть создан автономный пользователь для представления группы.

9.  Сотрите данные в окне запроса, введите следующую команду T-SQL и щелкните **Выполнить** на панели инструментов:

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    После выполнения этой команды автономный пользователь сможет создать базу данных.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Включение Azure AD в службе "Управляемый экземпляр Базы данных SQL Azure"

Управляемый экземпляр базы данных SQL Azure поддерживает создание базы данных с использованием управляемого удостоверения для ADF напрямую. Присоединять управляемое удостоверение для ADF к группе Azure AD или создавать автономного пользователя, представляющего эту группу в управляемом экземпляре, не требуется.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Настройка аутентификации Azure AD для управляемого экземпляра базы данных SQL Azure

1.   На портале Azure выберите **Все службы**  ->  **Серверы SQL Server** на левой панели навигации.

1.   Выберите управляемый экземпляр, для которого нужно настроить аутентификацию Azure AD.

1.   В разделе колонки **Параметры** выберите **Администратор Active Directory**.

1.   На панели команд щелкните **Задать администратора**.

1.   Выберите учетную запись пользователя Azure AD, которую необходимо сделать администратором сервера, а затем нажмите кнопку **Выбрать**.

1.   На панели команд нажмите кнопку **Сохранить**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Добавление управляемого удостоверения для ADF в качестве пользователя управляемого экземпляра базы данных SQL Azure

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Запустите SSMS.

2.  Подключитесь к управляемому экземпляру под учетной записью администратора SQL/Active Directory.

3.  В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

4.  Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

5.  Получение управляемого удостоверения для ADF. Чтобы получить ИДЕНТИФИКАТОР ПРИЛОЖЕНИЯ УДОСТОВЕРЕНИЯ СЛУЖБЫ субъекта, выполните инструкции, приведенные в статье [Удостоверение службы фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity). Не используйте для этой цели ИДЕНТИФИКАТОР УДОСТОВЕРЕНИЯ СЛУЖБЫ.

6.  В окне запроса выполните следующий сценарий T-SQL, чтобы преобразовать управляемое удостоверение для ADF в двоичный код:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your SERVICE IDENTITY APPLICATION ID}
    select CAST(@applicationId AS varbinary)
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF отобразится как двоичный код.

7.  Удалите данные в окне запроса и выполните следующий сценарий T-SQL, чтобы добавить управляемое удостоверение для ADF в качестве пользователя:

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID ={your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF получит возможность для создания базы данных.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Подготовка Azure SSIS IR на портале Azure или в приложении ADF

При подготовке Azure-SSIS IR на портале Azure или в приложении ADF на странице **Параметры SQL** установите флажок **Использовать аутентификацию AAD с управляемым удостоверением для ADF**. На приведенном ниже снимке экрана показаны параметры для среды выполнения интеграции, в которой SSISDB находится на сервере базы данных SQL Azure. Для среды выполнения интеграции, в которой SSISDB находится в управляемом экземпляре, параметры **Уровень службы базы данных каталога** и **Разрешить службам Azure доступ** не применяются, а остальные параметры будут такими же.

Дополнительные сведения о создании Azure-SSIS IR см. в статье [Создание среды выполнения интеграции Azure-SSIS в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Параметры среды выполнения интеграции Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Подготовка Azure-SSIS IR с помощью PowerShell

Чтобы подготовить к работе среду выполнения интеграции Azure-SSIS с помощью PowerShell, сделайте следующее:

1.  Установите модуль [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Не задавайте в скрипте параметр `CatalogAdminCredential`. Например: 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
