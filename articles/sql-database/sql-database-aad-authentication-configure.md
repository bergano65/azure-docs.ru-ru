---
title: Настройка аутентификации Azure Active Directory
description: Learn how to connect to SQL Database, managed instance, and SQL Data Warehouse by using Azure Active Directory Authentication - after configuring Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 11/06/2019
ms.openlocfilehash: 5830e0b7ee49a7d954dbdb3f897ee7ac5901c6a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421760"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Настройка и администрирование аутентификации Azure Active Directory с помощью SQL

This article shows you how to create and populate Azure AD, and then use Azure AD with Azure [SQL Database](sql-database-technical-overview.md), [managed instance](sql-database-managed-instance.md), and [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Обзор см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Эта статья относится к Azure SQL Server, а также к базам данных SQL и хранилища данных SQL, создаваемым на сервере SQL Azure. Для простоты база данных SQL используется как для базы данных SQL, так и для хранилища данных SQL.

> [!IMPORTANT]  
> Подключение к SQL Server на виртуальной машине Azure с использованием учетной записи Azure Active Directory не поддерживается. Вместо этого используйте учетную запись домена Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Создание и заполнение каталога Azure AD

Создайте каталог Azure AD и заполните его пользователями и группами. Azure AD может быть исходным управляемым доменом Azure AD. Azure AD может также быть локальной доменной службой Active Directory, объединенной в федерацию с Azure AD.

Дополнительные сведения см. в статьях [Интеграция локальных удостоверений с Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Добавление имени личного домена в Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure теперь поддерживает федерацию с Windows Server Active Directory), [Управление каталогом Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Azure Active Directory Cmdlets](/powershell/azure/overview) (Командлеты для Azure Active Directory) и [Порты и протоколы, необходимые для гибридной идентификации](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Связывание подписки Azure с Azure Active Directory или добавление ее в службу

1. Свяжите свою подписку Azure с Azure Active Directory, сделав каталог доверенным для подписки Azure, в которой размещена база данных. Дополнительные сведения см. в статье о [связи между подписками Azure и Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Используйте переключатель каталогов на портале Azure, чтобы перейти к подписке, связанной с предметной областью.

   > [!IMPORTANT]
   > Между каждой подпиской Azure и экземпляром Azure AD установлено отношение доверия. Это означает, что она доверяет каталогу проверять подлинность пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу, но одна конкретная подписка доверяет только одному каталогу. Данное отношение доверия, которое подписка имеет с каталогом, отличается от отношения, которую подписка имеет со всеми другими ресурсами в Azure (веб-сайтами, базами данных и т. д.), которые больше похожи на дочерние ресурсы подписки. Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Однако каталог останется в Azure, вы можете связать другую подписку с этим каталогом и продолжать управлять пользователями каталога. Дополнительные сведения о ресурсах см. в статье, посвященной [доступу к ресурсам в Azure](../active-directory/active-directory-b2b-admin-add-users.md). Дополнительные сведения об этих отношениях доверия см. в статье о [связывании подписки Azure с Azure Active Directory или добавлении ее в службу](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Создание администратора Azure AD для сервера Azure SQL Server

Каждый сервер Azure SQL Server (на котором размещена база данных SQL или хранилище данных SQL) сначала имеет одну учетную запись администратора сервера, который является администратором всего сервера Azure SQL Server. Необходимо создать второго администратора SQL Server, то есть учетную запись Azure AD. Этот участник создается как пользователь автономной базы данных в базе данных master. Администраторы с учетными записями администратора сервера являются членами роли **db_owner** в каждой пользовательской базе данных. Они входят в каждую такую базу данных как пользователи **dbo**. Дополнительные сведения о ролях администратора базы данных см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).

При использовании Azure Active Directory с георепликацией необходимо настроить администратора Azure Active Directory для сервера-источника и сервера-получателя. Если администратор Azure Active Directory для сервера отсутствует, то пользователи, входящие в Active Directory, будут получать сообщение о невозможности подключиться к серверу.

> [!NOTE]
> Пользователи без учетной записи Azure AD (включая учетную запись администратора Azure SQL Server) не могут создавать пользователей на основе Azure AD, так как у них нет разрешения на проверку предложенных пользователей базы данных с помощью Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provision an Azure Active Directory administrator for your managed instance

> [!IMPORTANT]
> Only follow these steps if you are provisioning a managed instance. This operation can only be executed by Global/Company administrator or a Privileged Role Administrator in Azure AD. Ниже описывается процесс предоставления разрешений пользователям, имеющим разные привилегии в каталоге.

> [!NOTE]
> For Azure AD admins for MI created prior to GA, but continue operating post GA, there is no functional change to the existing behavior. For more information, see the [New Azure AD admin functionality for MI](#new-azure-ad-admin-functionality-for-mi) section for more details.

Your managed instance needs permissions to read Azure AD to successfully accomplish tasks such as authentication of users through security group membership or creation of new users. For this to work, you need to grant permissions to managed instance to read Azure AD. Существуют два способа сделать это: с помощью портала и PowerShell. Ниже описываются оба способа.

1. В правом верхнем углу портала Azure щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory.

2. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию.

   С помощью этого шага можно связать подписку Active Directory с управляемым экземпляром, чтобы одна и та же подписка использовалась для Azure AD и управляемого экземпляра.

3. Перейдите к службе "Управляемый экземпляр Базы данных SQL" и выберите управляемый экземпляр, который вы хотите использовать для интеграции с Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Выберите баннер в верхней части страницы администрирования Active Directory и предоставьте разрешение текущему пользователю. If you're logged in as Global/Company administrator in Azure AD, you can do it from the Azure portal or using PowerShell with the script below.

    ![Предоставление разрешений на портале](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

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
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. После успешного завершения операции в правом верхнем углу появится следующее уведомление.

    ![Успешное завершение](./media/sql-database-aad-authentication/success.png)

6. Now you can choose your Azure AD admin for your managed instance. Для этого на странице "Администратор Active Directory" щелкните **Задать администратора**.

    ![Задание администратора](./media/sql-database-aad-authentication/set-admin.png)

7. На странице добавления администратора AAD найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**.

   На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. Список поддерживаемых администраторов приведен в разделе [Функции и ограничения Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Управление доступом на основе ролей (RBAC) применяется только к порталу Azure и не распространяется на SQL Server.

    ![Добавление администратора](./media/sql-database-aad-authentication/add-admin.png)

8. В верхней части страницы "Администратор Active Directory" нажмите кнопку **Сохранить**.

    ![Сохранить](./media/sql-database-aad-authentication/save.png)

    Изменение администратора может занять несколько минут. После этого новый администратор появится в поле "Администратор Active Directory".

After provisioning an Azure AD admin for your managed instance, you can begin to create Azure AD server principals (logins) with the <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> syntax. For more information, see [managed instance overview](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Чтобы потом удалить учетную запись администратора, в верхней части страницы "Администратор Active Directory" щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>New Azure AD admin functionality for MI

The table below summarizes the functionality for the public preview Azure AD login admin for MI, versus a new functionality delivered with GA for Azure AD logins.

| Azure AD login admin for MI during public preview | GA functionality for Azure AD admin for MI |
| --- | ---|
| Behaves in a similar way as Azure AD admin for SQL Database, which enables Azure AD authentication, but the Azure AD admin cannot create Azure AD or SQL logins in the master db for MI. | Azure AD admin has sysadmin permission and can create AAD and SQL logins in master db for MI. |
| Is not present in the sys.server_principals view | Is present in the sys.server_principals view |
| Allows individual Azure AD guest users to be set up as Azure AD admin for MI. For more information, see [Add Azure Active Directory B2B collaboration users in the Azure portal](../active-directory/b2b/add-users-administrator.md). | Requires creation of an Azure AD group with guest users as members to set up this group as an Azure AD admin for MI. For more information, see [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

As a best practice for existing Azure AD admins for MI created before GA, and still operating post GA, reset the Azure AD admin using the Azure portal “Remove admin” and “Set admin” option for the same Azure AD user or group.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Known issues with the Azure AD login GA for MI

- If an Azure AD login exists in the master database for MI, created using the T-SQL command `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, it can't be set up as an Azure AD admin for MI. You'll experience an error setting the login as an Azure AD admin using the Azure portal, PowerShell, or CLI commands to create the Azure AD login.
  - The login must be dropped in the master database using the command `DROP LOGIN [myaadaccount]`, before the account can be created as an Azure AD admin.
  - Set up the Azure AD admin account in the Azure portal after the `DROP LOGIN` succeeds. 
  - If you can't set up the Azure AD admin account, check in the master database of the managed instance for the login. Use the following command: `SELECT * FROM sys.server_principals`
  - Setting up an Azure AD admin for MI will automatically create a login in the master database for this account. Removing the Azure AD admin will automatically drop the login from the master database.

- Individual Azure AD guest users are not supported as Azure AD admins for MI. Guest users must be part of an Azure AD group to be set up as Azure AD admin. Currently, the Azure portal blade doesn't gray out guest users for another Azure AD, allowing users to continue with the admin setup. Saving guest users as an Azure AD admin will cause the setup to fail.
  - If you wish to make a guest user an Azure AD admin for MI, include the guest user in an Azure AD group, and set this group as an Azure AD admin.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell for SQL managed instance

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- Select-AzSubscription

Cmdlets used to provision and manage Azure AD admin for SQL managed instance:

| Имя командлета | Описание |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisions an Azure AD administrator for SQL managed instance in the current subscription. (Must be from the current subscription)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Removes an Azure AD administrator for SQL managed instance in the current subscription. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Returns information about an Azure AD administrator for SQL managed instance in the current subscription.|

The following command gets information about an Azure AD administrator for a managed instance named ManagedInstance01 that is associated with a resource group named ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

The following command provisions an Azure AD administrator group named DBAs for the managed instance named ManagedInstance01. This server is associated with resource group ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

The following command removes the Azure AD administrator for the managed instance named ManagedInstanceName01 associated with the resource group ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You can also provision an Azure AD admin for SQL managed instance by calling the following CLI commands:

| Команда | Описание |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisions an Azure Active Directory administrator for SQL managed instance. (Must be from the current subscription) |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Removes an Azure Active Directory administrator for SQL managed instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Returns information about an Azure Active Directory administrator currently configured for SQL managed instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Updates the Active Directory administrator for a SQL managed instance. |

For more information about CLI commands, see [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Подготовка администратора Azure Active Directory для сервера Базы данных SQL Azure

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете сервер Базы данных SQL Azure или хранилище данных Azure.

Ниже описаны две процедуры по подготовке администратора Azure Active Directory для сервера Azure SQL Server на портале Azure или с помощью PowerShell.

### <a name="azure-portal"></a>портала Azure

1. В правом верхнем углу [портала Azure](https://portal.azure.com/) щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. На этом шаге связанный с подпиской каталог Active Directory связывается с Azure SQL Server, чтобы одна и та же подписка использовалась для Azure AD и SQL Server. (На сервере Azure SQL Server может размещаться База данных SQL Azure или Хранилище данных SQL Azure.) ![choose-ad][8]

2. На баннере слева выберите **Все службы**, затем в поле фильтра введите **SQL Server**. Выберите **Серверы SQL Server**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > На этой странице, прежде чем выбрать **Серверы SQL Server**, можно щелкнуть **звезду** рядом с именем, чтобы добавить категорию в *Избранное* и добавить **серверы SQL Server** на левую панель навигации.

3. На странице **SQL Server** выберите **Администратор Active Directory**.

4. На странице **Администратор Active Directory** щелкните **Задать администратора**.

    ![выбор Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. На странице **Добавление администратора** найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**. На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. (See the list of supported admins in the **Azure AD Features and Limitations** section of [Use Azure Active Directory Authentication for authentication with SQL Database or SQL Data Warehouse](sql-database-aad-authentication.md).) Role-based access control (RBAC) applies only to the portal and is not propagated to SQL Server.

    ![Выбор администратора](./media/sql-database-aad-authentication/select-admin.png)  

6. В верхней части страницы **Администратор Active Directory** нажмите кнопку **Сохранить**.

    ![save admin](./media/sql-database-aad-authentication/save-admin.png)

Изменение администратора может занять несколько минут. После этого новый администратор появится в поле **Администратор Active Directory** .

   > [!NOTE]
   > При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в виртуальной базе данных master как пользователь аутентификации SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Такой пользователь аутентификации SQL Server не входит в Azure AD, поэтому любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится сбоем.

Чтобы потом удалить учетную запись администратора, в верхней части страницы **Администратор Active Directory** щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell for Azure SQL Database and Azure SQL Data Warehouse

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- Select-AzSubscription

Cmdlets used to provision and manage Azure AD admin for Azure SQL Database and Azure SQL Data Warehouse:

| Имя командлета | Описание |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Must be from the current subscription) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |

Use PowerShell command get-help to see more information for each of these commands. Пример: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Приведенный ниже скрипт выполняет подготовку группы администраторов Azure AD с именем **DBA_Group** (идентификатор объекта `40b79501-b343-44ed-9ce7-da4c8cc7353f`) для сервера **demo_server** в группе ресурсов с именем **Group-23**.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Входной параметр **DisplayName** принимает отображаемое имя Azure AD или имя участника-пользователя. Например, ``DisplayName="John Smith"`` и ``DisplayName="johns@contoso.com"``. Для групп Azure AD поддерживается только отображаемое имя Azure AD.

> [!NOTE]
> Команда Azure PowerShell ```Set-AzSqlServerActiveDirectoryAdministrator``` не запрещает подготовку администраторов Azure AD для неподдерживаемых пользователей. Неподдерживаемого пользователя можно подготовить, но он не сможет подключиться к базе данных (См.

В следующем примере используется необязательный параметр **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Параметр **ObjectID** Azure AD является обязательным, если параметр **DisplayName** не уникален. Чтобы получить значения **ObjectID** и **DisplayName**, используйте раздел Active Directory классического портала Azure. Там можно просмотреть свойства пользователя или группы.

Следующий пример возвращает сведения о текущем администраторе Azure AD для сервера Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

В примере ниже выполняется удаление администратора Azure AD.

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You can provision an Azure AD admin by calling the following CLI commands:

| Команда | Описание |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Must be from the current subscription) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Обновляет учетную запись администратора Active Directory для сервера SQL Server Azure или хранилища данных SQL Azure. |

For more information about CLI commands, see [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Кроме того, можно подготовить администратора Azure Active Directory с помощью интерфейсов REST API. Дополнительные сведения см. в справочнике по REST API управления службами и статье [Operations for Azure SQL Databases](/rest/api/sql/) (Операции для Базы данных SQL Azure).

## <a name="configure-your-client-computers"></a>Настройка клиентских компьютеров

Приложения или пользователи подключаются к Базе данных SQL Azure или хранилищу данных SQL, используя удостоверения Azure AD, с клиентских компьютеров. На эти компьютеры необходимо установить указанное ниже программное обеспечение.

- .NET Framework 4.6 или более поздней версии с [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Библиотека аутентификации Azure Active Directory для SQL Server (*ADALSQL.DLL*) доступна на нескольких языках (версии x86 и amd64) в центре загрузки в [библиотеке аутентификации Microsoft Active Directory для Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Вы можете выполнить эти требования, сделав следующее:

- Установка [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) или [SQL Server Data Tools для Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) соответствует требованиям .NET Framework 4.6.
- SSMS устанавливает *ADALSQL.DLL*версии x86.
- SSDT устанавливает *ADALSQL.DLL*версии amd64.
- Последняя версия Visual Studio со страницы [скачивания Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) соответствует требованиям .NET Framework 4.6, но не устанавливает необходимую версию amd64 *ADALSQL.DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD

> [!IMPORTANT]
> Managed instance now supports Azure AD server principals (logins), which enables you to create logins from Azure AD users, groups, or applications. Azure AD server principals (logins) provides the ability to authenticate to your managed instance without requiring database users to be created as a contained database user. For more information, see [managed instance Overview](sql-database-managed-instance.md#azure-active-directory-integration). Сведения о синтаксисе для создания субъектов сервера (имен для входа) Azure AD см. в статье <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN (Transact-SQL)</a>.

Для проверки подлинности Azure Active Directory необходимо, чтобы пользователи базы данных создавались как пользователи автономной базы данных. Пользователь автономной базы данных на основе удостоверения Azure AD —это пользователь, у которого нет имени для входа в базу данных master и который сопоставляется с удостоверением в каталоге Azure AD, связанном с базой данных. Удостоверение Azure AD может быть учетной записью отдельного пользователя или группы. Дополнительные сведения о пользователях автономной базы данных см. в статье [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Пользователей баз данных (за исключением администраторов) невозможно создавать с помощью портала Azure. Роли RBAC не распространяются в SQL Server, Базу данных SQL или хранилище данных SQL. Роли Azure RBAC используются для управления ресурсами Azure и не относятся к разрешениям базы данных. Например, роль **Участник SQL Server** не предоставляет разрешение на подключение к Базе данных SQL или хранилищу данных SQL. Разрешение доступа должно быть предоставлено непосредственно в базе данных с помощью инструкций Transact-SQL.

> [!WARNING]
> Специальные символы, такие как двоеточие `:` или амперсанд `&`, когда они включены в качестве имен пользователей в операторах T-SQL CREATE LOGIN и CREATE USER, не поддерживаются.

Вы можете создать пользователя автономной базы данных на основе Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD от имени пользователя с уровнем разрешений не ниже, чем **Изменение любого пользователя**. Затем используйте следующий синтаксис Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

Значением параметра *Azure_AD_principal_name* может быть имя участника-пользователя Azure AD или отображаемое имя группы Azure AD.

**Примеры.** Создание пользователя автономной базы данных, представляющего собой пользователя федеративного или управляемого домена Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, представляющего собой группу Azure AD или группу федеративного домена, укажите отображаемое имя группы безопасности.

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Чтобы создать пользователя автономной базы данных, который представляет приложение и будет подключаться с помощью маркера Azure AD, выполните следующее.

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> This command requires that SQL access Azure AD (the "external provider") on behalf of the logged-in user. Sometimes, circumstances will arise that cause Azure AD to return an exception back to SQL. In these cases, the user will see SQL error 33134, which should contain the AAD-specific error message. Most of the time, the error will say that access is denied, or that the user must enroll in MFA to access the resource, or that access between first-party applications must be handled via preauthorization. In the first two cases, the issue is usually caused by Conditional Access policies that are set in the user's AAD tenant: they prevent the user from accessing the external provider. Updating the CA policies to allow access to the application '00000002-0000-0000-c000-000000000000' (the application ID of the AAD Graph API) should resolve the issue. In the case that the error says access between first-party applications must be handled via preauthorization, the issue is because the user is signed in as a service principal. The command should succeed if it is executed by a user instead.

> [!TIP]
> Создать пользователя напрямую из каталога Azure Active Directory можно только в том случае, если этот каталог связан с вашей подпиской Azure. Однако членов других каталогов Active Directory, являющихся импортированными пользователями в связанном каталоге Active Directory (так называемыми внешними пользователями), можно добавить в группу Active Directory в клиенте Active Directory. Создав пользователя автономной базы данных для этой группы AD, пользователи из внешнего каталога Active Directory могут получить доступ к базе данных SQL.

Подробные сведения о создании пользователей автономной базы данных на основе удостоверений Azure Active Directory см. в статье [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Если удалить учетную запись администратора Azure Active Directory на сервере Azure SQL Server, все прошедшие аутенитификацию пользователи Azure AD не смогут подключиться к серверу. При необходимости администратор Базы данных SQL может вручную удалить неиспользуемых пользователей Azure AD.

> [!NOTE]
> При получении ответа **Истек тайм-аут подключения**, возможно, потребуется задать для параметра `TransparentNetworkIPResolution` в строке подключения значение false. Дополнительные сведения см. в статье [Connection timeout issue with .NET Framework 4.6.1 — TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Проблема с временем ожидания подключения в .NET Framework 4.6.1 — TransparentNetworkIPResolution).

При создании пользователь базы данных получает разрешение **Подключение** и может подключаться к этой базе данных как участник роли **Public**. Изначально пользователю доступны только разрешения, предоставленные роли **Public**, или другие разрешения, предоставленные любой группе Azure AD, в которую входит пользователь. Подготовив пользователя автономной базы данных, использующей Azure AD, можно предоставить ему дополнительные разрешения — так же, как разрешения для любого другого типа пользователя. Обычно разрешения предоставляются ролям базы данных, а затем эти роли назначаются пользователям. Подробные сведения см. в статье [Database Engine Permission Basics](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) (Основные сведения о разрешениях ядра СУБД). Дополнительные сведения о специальных ролях базы данных SQL см. в статье [Проверка подлинности и авторизация в базе данных SQL Azure: предоставление доступа](sql-database-manage-logins.md).
Для учетной записи пользователя федеративного домена, импортируемая в управляемый домен, следует использовать идентификатор управляемого домена.

> [!NOTE]
> Пользователи Azure AD помечаются в метаданных базы данных как тип E (EXTERNAL_USER), а группы — как тип X (EXTERNAL_GROUPS). Дополнительные сведения см. в статье [sys.database_principals (Transact-SQL)](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Подключение к пользовательской базе данных или хранилищу данных с помощью SSMS или SSDT  

Чтобы убедиться, что администратор Azure AD настроен правильно, подключитесь к базе данных **master** с помощью учетной записи администратора Azure AD.
Вы можете подготовить пользователя автономной базы данных, использующей Azure AD (кроме администратора сервера, который является владельцем базы данных). Для этого подключитесь к базе данных с помощью удостоверения Azure AD, у которого есть доступ к базе данных.

> [!IMPORTANT]
> Проверку подлинности Azure Active Directory поддерживают [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) для Visual Studio 2015. Выпуск SSMS за август 2016 года также включает в себя поддержку универсальной аутентификации Active Directory, что позволяет администраторам требовать прохождения многофакторной проверки подлинности с помощью телефонного звонка, текстового сообщения, смарт-карты с ПИН-кодом или уведомления в мобильном приложении.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Использование удостоверения Azure AD для подключения с помощью SSMS или SSDT

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD с использованием SQL Server Management Studio или инструментов баз данных SQL Server.

### <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Этот метод следует использовать, если вы входите в систему Windows с помощью учетных данных Azure Active Directory из федеративного домена.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — встроенная**. Пароль не требуется (и его нельзя ввести), так как для подключения используются существующие учетные данные.

    ![Выбор встроенной аутентификации Active Directory][11]

2. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (Параметр **Доменное имя AD или идентификатор клиента** поддерживается только для параметра **Universal with MFA connection** (Универсальная с подключением MFA), в противном случае он неактивен.)  

    ![Выбор имени базы данных][13]

## <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Используйте этот метод при подключении с помощью имени участника Azure AD, в котором используется управляемый домен Azure AD. Его также можно использовать для федеративных учетных записей без доступа к домену, например при удаленной работе.

Используйте этот способ для проверки подлинности в базе данных или хранилище данных SQL с Azure AD для собственных федеративных пользователей Azure AD. Собственные пользователи являются единственным исключением, созданным в Azure AD, для которых производится проверка подлинности с помощью имени и пароля, в то время как федеративный пользователь является пользователем Windows, чей домен объединен с Azure AD. Второй способ (использование пользователя и пароля) можно использовать, когда пользователю необходимо войти с учетными данными Windows, но локальный компьютер не присоединен к домену (при использовании, например, удаленного доступа). В этом случае пользователь Windows может указать учетную запись и пароль домена и пройти проверку подлинности в базе данных или хранилище данных SQL с помощью федеративных учетных данных.

1. Запустите Management Studio или Data Tools и в диалоговом окне **Подключение к серверу** (или **Подключиться к ядру СУБД**) в поле **Проверка подлинности** выберите **Active Directory — пароль**.

2. In the **User name** box, type your Azure Active Directory user name in the format **username\@domain.com**. Именем пользователя должна быть учетная запись из Azure Active Directory или учетная запись из федерации домена с Azure Active Directory.

3. В поле **Пароль** введите пароль пользователя для учетной записи Azure Active Directory или учетной записи федеративного домена.

    ![Выбор проверки пароля Active Directory][12]

4. Нажмите кнопку **Параметры**, а затем на странице **Свойства подключения** в поле **Подключение к базе данных** введите имя пользовательской базы данных для подключения. (См. рисунок в предыдущем варианте.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Использование удостоверения Azure AD для подключения из клиентского приложения

Ниже описаны действия по подключению к базе данных SQL с помощью удостоверения Azure AD из клиентского приложения.

### <a name="active-directory-integrated-authentication"></a>Встроенная аутентификация Active Directory

Для использования встроенной проверки подлинности Windows необходимо включить в федерацию Active Directory вашего домена и Azure Active Directory. Клиентское приложение (или служба), подключающееся к базе данных, должно быть запущено на компьютере, присоединенном к домену, с учетными данными пользователя домена.

Чтобы подключиться к базе данных с помощью встроенной проверки подлинности и удостоверения Azure AD, ключевое слово проверки подлинности в строке подключения к базе данных должно иметь значение «Active Directory Integrated». В следующем примере кода C# используется ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Ключевое слово `Integrated Security=True` строки подключения не используется для подключения к базе данных SQL Azure. При создании подключения ODBC потребуется удалить пробелы и задать для параметра Authentication значение ActiveDirectoryIntegrated.

### <a name="active-directory-password-authentication"></a>Проверка пароля Active Directory

Чтобы подключиться к базе данных с помощью встроенной аутентификации и удостоверения Azure AD, ключевому слову аутентификации нужно присвоить пароль Active Directory. Строка подключения должна содержать ключевые слова для идентификатора пользователя (UID) и пароля (PWD), а также их значения. В следующем примере кода C# используется ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Дополнительные сведения о методах проверки подлинности Azure AD и примеры кода вы найдете в [демонстрации проверки подлинности Azure AD на GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Токен Azure AD

Этот метод проверки подлинности позволяет службам среднего уровня подключаться к базе данных SQL Azure или хранилищу данных SQL Azure путем получения маркера из Azure Active Directory (AAD). Он используется для реализации сложных сценариев, включая проверку подлинности на основе сертификатов. Для работы с проверкой подлинности маркера Azure AD необходимо выполнить четыре основных шага.

1. Register your application with Azure Active Directory and get the client ID for your code.
2. Создайте пользователя базы данных, представляющего приложение. (Выполнено ранее на шаге 6.)
3. Создайте сертификат на клиентском компьютере, на котором выполняется приложение.
4. Добавьте сертификат в качестве ключа для вашего приложения.

Пример строки подключения.

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Дополнительные сведения см. в [блоге о безопасности SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Дополнительные сведения о добавлении сертификата см. в статье [Приступая к работе с аутентификацией на основе сертификата в Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Ниже приведены инструкции для подключения с помощью sqlcmd версии 13.1, доступной в [Центре загрузки](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` with the `-G` command does not work with system identities, and requires a user principal login.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о доступе к базе данных SQL и управлении ею см. в статье [Контроль доступа к базе данных SQL Azure](sql-database-control-access.md).
- Общие сведения об именах для входа, пользователях и ролях базы данных в базе данных SQL см. в статье [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md).
- Дополнительные сведения о субъектах базы данных см. в [этой статье](https://msdn.microsoft.com/library/ms181127.aspx).
- Дополнительные сведения о ролях баз данных см. в статье [Роли уровня базы данных](https://msdn.microsoft.com/library/ms189121.aspx).
- Дополнительные сведения о правилах брандмауэра см. в статье [Обзор правил брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
