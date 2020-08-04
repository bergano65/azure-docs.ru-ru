---
title: Создание пользователей Azure AD с помощью субъектов-служб
description: В этом учебнике приведены сведения по созданию пользователей Azure AD с помощью приложений Azure AD (субъект-службы) в службе "База данных SQL Azure" и Azure Synapse Analytics.
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 10d1fe9e47b7a3bf2d921f86703d5ae6d067813c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293517"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Руководство по созданию пользователей Azure AD с помощью приложений Azure AD

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Эта статья предоставляется в режиме **общедоступной предварительной версии**. Дополнительные сведения см. в статье [Субъект-служба Azure Active Directory с SQL Azure](authentication-aad-service-principal.md). В этой статье для демонстрации основных шагов будет использоваться служба "База данных SQL Azure", однако эти инструкции можно применять к [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

В этой статье описывается процесс создания пользователей Azure AD в службе "База данных SQL Azure" с помощью субъектов-служб Azure (приложения Azure AD). Эта функция уже существует в Управляемом экземпляре SQL Azure, но теперь она появилась в службе "База данных SQL Azure" и Azure Synapse Analytics. Для поддержки этого сценария необходимо создать удостоверение Azure AD и назначить его логическому серверу SQL Azure.

Дополнительные сведения о проверке подлинности Azure AD для SQL Azure см. в статье [Использование проверки подлинности Azure Active Directory](authentication-aad-overview.md).

В этом руководстве описано следующее:

> [!div class="checklist"]
> - Назначение удостоверения для логического сервера SQL Azure.
> - Назначение разрешения читателей каталогов для удостоверения логического сервера SQL.
> - Создание субъекта-службы (приложение Azure AD) в Azure AD.
> - Создание пользователя субъекта-службы в службе "База данных SQL Azure".
> - Создание другого пользователя Azure AD в службе "База данных SQL" с помощью пользователя субъекта-службы Azure AD.

## <a name="prerequisites"></a>Предварительные требования

- Существующая развернутая служба [База данных SQL Azure](single-database-create-quickstart.md) или [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). В этом учебнике предполагается, что вы используете службу "База данных SQL".
- Доступ к уже существующей Azure Active Directory.
- Для настройки отдельного приложения Azure AD в качестве администратора Azure AD для SQL Azure с помощью PowerShell необходим модуль [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) или более поздней версии. Убедитесь, что используется последняя версия модуля.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Назначение удостоверения для логического сервера SQL Azure

1. Подключитесь к Azure Active Directory. Необходимо будет найти идентификатор клиента. Для этого перейдите на [портал Azure](https://portal.azure.com) и выберите ресурс **Azure Active Directory**. В области **Обзор** отобразится **идентификатор клиента**. Выполните следующую команду PowerShell:

    - Замените `<TenantId>` своим **идентификатором клиента**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Запишите `TenantId` для будущего использования в этом учебнике.

1. Создайте и назначьте удостоверение Azure AD логическому серверу SQL Azure. Выполните указанную ниже команду PowerShell:

    - Замените `<resource group>` и `<server name>` своими ресурсами. Если имя сервера `myserver.database.windows.net`, замените `<server name>` на `myserver`.

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Дополнительные сведения см. в описании команды [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver).

    > [!IMPORTANT]
    > Если удостоверение Azure AD настроено для логического сервера SQL Azure, то ему должно быть предоставлено разрешение [**читателей каталога**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers). Этот шаг будет рассмотрен в следующем разделе. **Не** пропустите этот шаг, так как проверка подлинности Azure AD перестанет работать.

    - Если вы ранее использовали команду [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) с параметром `AssignIdentity` для создания SQL Server, вам потребуется выполнить команду [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) в качестве отдельной команды, чтобы включить это свойство в структуре Azure.

1. Убедитесь, что удостоверение сервера успешно назначено. Выполните указанную ниже команду PowerShell:

    - Замените `<resource group>` и `<server name>` своими ресурсами. Если имя сервера `myserver.database.windows.net`, замените `<server name>` на `myserver`.
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    В выходных данных должны отобразиться `PrincipalId`, `Type` и `TenantId`. `PrincipalId` — это присваиваемое удостоверение.

1. Вы также можете проверить удостоверение на [портале Azure](https://portal.azure.com).

    - В области ресурса **Azure Active Directory** выберите **Корпоративные приложения**. Введите имя логического сервера SQL. Вы увидите, что у него есть **идентификатор объекта**, присоединенный к ресурсу.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Назначение разрешения читателей каталогов для удостоверения логического сервера SQL

Чтобы обеспечить правильную работу назначенного удостоверения Azure AD для SQL Azure, удостоверению сервера необходимо предоставить разрешение `Directory Readers` Azure AD.

Чтобы предоставить это требуемое разрешение, выполните указанный ниже скрипт.

> [!NOTE] 
> Этот скрипт должен выполняться `Global Administrator` или `Privileged Roles Administrator` Azure AD.

- Замените `<TenantId>` на `TenantId`, собранный ранее.
- Замените `<server name>` именем логического сервера SQL. Если имя сервера `myserver.database.windows.net`, замените `<server name>` на `myserver`.

```powershell
# This script grants Azure “Directory Readers” permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or “Privileged Roles Administrator” type of user.
# To check if the “Directory Readers" permission was granted, execute this script again

Import-Module AzureAd
connect-azuread -TenantId "<TenantId>"     #Enter your actual TenantId
 $AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null)
{
    Write-Output "Error: No Service Principals with name '$    ($AssignIdentityName)', make sure that AssignIdentityName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1))
{
    Write-Output "Error: More than one service principal with name pattern '$    ($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}
 
if ($selDirReader -eq $null)
{
    # Add principal to readers role
    Write-Output "Adding service principal '$($msName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to     'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else
{
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Выходные данные в приведенном выше скрипте указывают, предоставлено ли удостоверению разрешение читателей каталога. Если вы не уверены, предоставлено ли разрешение, повторно выполните скрипт.

Вы можете ознакомиться с аналогичным подходом по установке разрешений **читателей каталога** для Управляемого экземпляра SQL в статье [Подготовка администратора Azure AD (Управляемый экземпляр SQL)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Создание субъекта-службы (приложение Azure AD) в Azure AD

1. Следуйте указаниям в этом разделе, чтобы [зарегистрировать приложение и задать разрешения](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Обязательно добавьте **разрешения приложения**, а также **делегированные разрешения**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="aad-apps":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. Вам также необходимо будет создать секрет клиента для входа. Следуйте указаниям в этом разделе, чтобы [отправить сертификат или создать секрет для входа](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in).

3. Запишите следующие сведения из регистрации приложения. Они должны быть доступны на панели **Обзор**.
    - **Идентификатор приложения**
    - **Идентификатор клиента**. Он должен остаться таким же, как прежде.

В этом учебнике в качестве основного субъекта-службы мы будем использовать *AppSP*, а *myapp* — в качестве второго пользователя субъекта-службы, который будет создан в SQL Azure с помощью *AppSP*. Необходимо создать два приложения: *AppSP* и *myapp*.

Дополнительные сведения о создании приложения Azure AD см. в статье [Практическое руководство. Создание приложения Azure Active Directory и субъект-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md).


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Создание пользователя субъекта-службы в службе "База данных SQL Azure"

После создания субъекта-службы в Azure AD создайте пользователя в службе "База данных SQL". Необходимо подключиться к службе "База данных SQL" с действующим именем входа с разрешениями на создание пользователей в базе данных.

1. Создайте пользователя *AppSP* в службе "База данных SQL", выполнив указанную ниже команду T-SQL.

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Предоставьте для *AppSP* разрешение `db_owner`, которое позволяет пользователю создавать других пользователей Azure AD в базе данных.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Дополнительные сведения см. в статье [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    Кроме того, вместо назначения роли `db_owner` можно предоставить разрешение `ALTER ANY USER`. Это позволит субъекту-службе добавлять других пользователей Azure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > Указанный выше параметр не требуется, если *AppSP* установлен в качестве администратора Azure AD для сервера. Установить субъект-службу в качестве администратора AD для логического сервера SQL можно с помощью портала Azure, PowerShell или команд Azure CLI. Дополнительные сведения см. в разделе о [подготовке администратора Azure AD (служба "База данных SQL")](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Создание пользователя Azure AD в службе "База данных SQL" с помощью субъекта-службы Azure AD

> [!IMPORTANT]
> Субъект-служба, используемая для входа в службу "База данных SQL", должна иметь секрет клиента. Если он отсутствует, выполните шаг 2 раздела по [созданию субъекта-службы (приложение Azure AD) в Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Этот секрет клиента необходимо добавить в указанный ниже скрипт в качестве входного параметра.

1. Используйте следующий скрипт, чтобы создать пользователя субъекта-службы Azure AD *myapp* с помощью субъекта-службы *AppSP*.

    - Замените `<TenantId>` на `TenantId`, собранный ранее.
    - Замените `<ClientId>` на `ClientId`, собранный ранее.
    - Замените `<ClientSecret>` секретом клиента, созданным ранее.
    - Замените `<server name>` именем логического сервера SQL. Если имя сервера `myserver.database.windows.net`, замените `<server name>` на `myserver`.
    - Замените `<database name>` именем службы "База данных SQL".

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    Кроме того, вы можете использовать пример кода, приведенный в блоге, посвященном [проверке подлинности субъекта-службы Azure AD в базе данных SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Измените скрипт, чтобы выполнить инструкцию DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER`. Этот же скрипт можно использовать для создания обычной группы пользователей Azure AD в службе "Базе данных SQL".

    > [!NOTE]
    > Если необходимо установить модуль AzureRM.profile, откройте PowerShell от имени администратора. Для автоматической установки последней версии AzureRM.profile и установки `$adalpath` для приведенного выше скрипта выполните следующие команды:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Убедитесь, что в базе данных существует пользователь *myapp*, выполнив следующую команду:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Вы должны увидеть похожие выходные данные:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Дальнейшие действия

- [Субъект-служба Azure Active Directory с SQL Azure](authentication-aad-service-principal.md)
- [Что такое управляемые удостоверения для ресурсов Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Использование управляемых удостоверений в Службе приложений и Функциях Azure](../../app-service/overview-managed-identity.md)
- [Проверка подлинности субъекта-службы Azure AD в базе данных SQL — пример кода](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Application and service principal objects in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) (Объекты приложения и субъекта-службы в Azure Active Directory)
- [Создание субъекта-службы Azure с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)