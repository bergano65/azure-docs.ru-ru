---
title: Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS | Документация Майкрософт
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
ms.openlocfilehash: a67436f09d6e28db8d19679e446ac4cf98383709
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593803"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Включение аутентификации Azure Active Directory для среды выполнения интеграции Azure-SSIS

В этой статье показано, как включить аутентификацию Azure Active Directory (Azure AD) с управляемым удостоверением, для вашей фабрики данных Azure (ADF) и использовать его вместо проверки подлинности SQL, чтобы создать среду Azure-SSIS выполнения интеграции (IR), в свою очередь предоставит Базу данных каталога SSIS (SSISDB) в базе данных SQL server или управляемого экземпляра от вашего имени.

Сведения о настройке управляемого удостоверения для ADF-ФАЙЛЕ, см. в разделе [identiy управляемый для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  В этом сценарии проверки подлинности Azure AD с управляемым удостоверением для ADF-ФАЙЛЕ используется только при создании и последующих начала операции среду SSIS IR, который будет в Включение подготовки и подключения к базе данных SSISDB. Для выполнения пакетов служб SSIS среду SSIS IR будет по-прежнему подключаться к SSISDB, используя проверку подлинности SQL с помощью полностью управляемых учетных записей, созданных во время подготовки SSISDB.
>-  Если вы уже создали среду SSIS IR с проверкой подлинности SQL, не можно перенастроить его для использования проверки подлинности Azure AD с помощью PowerShell в настоящее время, но это можно сделать с помощью приложения портала/ADF-Azure. 

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

3.  Добавьте в группу управляемое удостоверение службы "Фабрика данных Azure". Статьи можно выполнить [identiy управляемый для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) для получения участника управляемого удостоверения идентификатор объекта (например 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, но не использует управляемые идентификатор приложения удостоверения для этой цели).

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

5.  Выберите учетную запись пользователя Azure AD, сделать администратором сервера, а затем нажмите кнопку **выберите.**

6.  На панели команд нажмите кнопку **Сохранить**.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Создание на сервере Базы данных SQL Azure автономного пользователя, представляющего группу Azure AD

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1. Запустите SSMS.

2. В **соединение с сервером** диалоговое окно, введите имя сервера базы данных SQL Azure в **имя_сервера** поля.

3. В **проверки подлинности** выберите **Active Directory — универсальная с поддержкой MFA** (можно также использовать другой двух типов проверки подлинности Active Directory, см. в разделе [Настройка и управление ими Проверка подлинности Azure AD с помощью SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. В **имя пользователя** введите имя учетной записи Azure AD, установленной в качестве администратора сервера, например testuser@xxxonline.com.

5. Выберите **Connect** и завершите процесс входа в систему.

6. В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

7. Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

8. В окне запроса введите следующую команду T-SQL и выберите **Execute** на панели инструментов.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   В результате выполнения команды должен быть создан автономный пользователь для представления группы.

9. Сотрите данные в окне запроса, введите следующую команду T-SQL и щелкните **Выполнить** на панели инструментов:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   После выполнения этой команды автономный пользователь сможет создать базу данных (SSISDB).

10. Если необходимо переключиться на использование проверки подлинности Azure AD для вашей среды выполнения Интеграции Azure SSIS к нему доступ к SSISDB был создан с помощью проверки подлинности SQL, щелкните правой кнопкой мыши **SSISDB** базы данных и выберите **новый запрос**.

11. В окне запроса введите следующую команду T-SQL и выберите **Execute** на панели инструментов.

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

1.   На портале Azure выберите **Все службы**  ->  **Серверы SQL Server** на левой панели навигации.

2.   Выберите управляемый экземпляр, для которого нужно настроить аутентификацию Azure AD.

3.   В разделе колонки **Параметры** выберите **Администратор Active Directory**.

4.   На панели команд щелкните **Задать администратора**.

5.   Выберите учетную запись пользователя Azure AD, которую необходимо сделать администратором сервера, а затем нажмите кнопку **Выбрать**.

6.   На панели команд нажмите кнопку **Сохранить**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Добавление управляемого удостоверения для ADF в качестве пользователя управляемого экземпляра Базы данных SQL Azure

На следующем шаге вам потребуется  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS).

1.  Запустите SSMS.

2.  Подключитесь к управляемому экземпляру под учетной записью администратора SQL/Active Directory.

3.  В **обозревателе объектов** разверните папку **Базы данных**  ->  **Системные базы данных**.

4.  Щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

5.  Получение управляемого удостоверения для ADF. Можно выполнить статьи [identiy управляемый для фабрики данных](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) для get управляемого удостоверения приложения идентификатор участника (но не используйте управляемые идентификатор объекта удостоверения для этой цели).

6.  В окне запроса выполните следующий сценарий T-SQL, чтобы преобразовать управляемое удостоверение для ADF в двоичный код:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF отобразится как двоичный код.

7.  Удалите данные в окне запроса и выполните следующий сценарий T-SQL, чтобы добавить управляемое удостоверение для ADF в качестве пользователя:

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    После выполнения этой команды управляемое удостоверение для ADF получит возможность для создания базы данных (SSISDB).

8.  Если необходимо переключиться на использование проверки подлинности Azure AD для вашей среды выполнения Интеграции Azure SSIS к нему доступ к SSISDB был создан с помощью проверки подлинности SQL, щелкните правой кнопкой мыши **SSISDB** базы данных и выберите **новый запрос**.

9.  В окне запроса введите следующую команду T-SQL и выберите **Execute** на панели инструментов.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
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
