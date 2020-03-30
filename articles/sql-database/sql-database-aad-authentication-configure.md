---
title: Настройка аутентификации Azure Active Directory
description: Узнайте, как подключиться к базе данных S'L, управляемой примеру, и хранилищем данных СЗЛ с помощью azure Active Directory Authentication - после настройки Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: 881c7076d5131746c730757a07da6fb938429c38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125037"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Настройка и администрирование аутентификации Azure Active Directory с помощью SQL

В этой статье показано, как создавать и заполнять Azure AD, а затем использовать Azure AD с базой данных Azure [S'L,](sql-database-technical-overview.md) [управляемым экземпляром](sql-database-managed-instance.md)и [хранилищем данных S'L.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Обзор см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

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

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Предоставление администратора активного каталога Azure для управляемого экземпляра

> [!IMPORTANT]
> Только следуйте этим шагам, если вы предоставляете управляемый экземпляр. Эта операция может быть выполнена только администратором Global/Company или привилегированным администратором ролей в Azure AD. Ниже описывается процесс предоставления разрешений пользователям, имеющим разные привилегии в каталоге.

> [!NOTE]
> Для admins AD Azure для MI, созданных до GA, но продолжающих работу после GA, функциональных изменений в существующем поведении не происходит. Для получения дополнительной информации можно ознакомиться с [функциональностью ad-функции New Azure Ad для](#new-azure-ad-admin-functionality-for-mi) раздела MI для получения более подробной информации.

Управляемым экземплярам нужны разрешения для чтения Azure AD для успешного выполнения таких задач, как аутентификация пользователей через членство в группе безопасности или создание новых пользователей. Чтобы это сработало, необходимо предоставить разрешения управляемому экземпляру на чтение Azure AD. Существуют два способа сделать это: с помощью портала и PowerShell. Ниже описываются оба способа.

1. В правом верхнем углу портала Azure щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory.

2. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию.

   С помощью этого шага можно связать подписку Active Directory с управляемым экземпляром, чтобы одна и та же подписка использовалась для Azure AD и управляемого экземпляра.

3. Перейдите к службе "Управляемый экземпляр Базы данных SQL" и выберите управляемый экземпляр, который вы хотите использовать для интеграции с Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Выберите баннер в верхней части страницы администрирования Active Directory и предоставьте разрешение текущему пользователю. Если вы вошли в систему в качестве администратора Global/Company в Azure AD, вы можете сделать это с портала Azure или с помощью PowerShell со сценарием ниже.

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

6. Теперь вы можете выбрать админ Azure AD для управляемого экземпляра. Для этого на странице "Администратор Active Directory" щелкните **Задать администратора**.

    ![Задание администратора](./media/sql-database-aad-authentication/set-admin.png)

7. На странице добавления администратора AAD найдите пользователя, выберите пользователя (или группу), чтобы назначить его администратором, и щелкните **Выбрать**.

   На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. Список поддерживаемых администраторов приведен в разделе [Функции и ограничения Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Управление доступом на основе ролей (RBAC) применяется только к порталу Azure и не распространяется на SQL Server.

    ![Добавить admin-реаджура Active Directory Azure](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. В верхней части страницы Администратор Active Directory нажмите кнопку **Сохранить**.

    ![Сохранить](./media/sql-database-aad-authentication/save.png)

    Изменение администратора может занять несколько минут. После этого новый администратор появится в поле "Администратор Active Directory".

После подготовки администратора Azure AD для управляемого экземпляра можно приступить к созданию принципов сервера AD Azure AD с помощью синтаксиса <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a> Для получения дополнительной информации смотрите [обзор управляемого экземпляра](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Чтобы потом удалить учетную запись администратора, в верхней части страницы Администратор Active Directory щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Новая функция админ-амин Azure для MI

В приведенной ниже таблице приведены итоги функциональности для общедоступного просмотра ad-ад-а-ина для MI по сравнению с новой функциональностью, поставляемой с помощью логинов GA для Azure AD.

| AD-ад-ин-админ Для MI во время публичного предварительного просмотра | Функциональность GA для админ-админ Azure AD для MI |
| --- | ---|
| Ведет себя так же, как и admin Azure AD для базы данных S'L, которая позволяет аутентифицировать AD Azure, но админ Azure AD не может создавать логины Azure AD или S'L в мастер-дБ для MI. | Admin Azure AD имеет разрешение sysadmin и может создавать логины AAD и S'L в master db для MI. |
| Не присутствует в sys.server_principals view | Присутствует в представлении sys.server_principals |
| Позволяет настроить отдельных гостевых пользователей Azure AD в качестве админ-админ Azure AD для MI. Для получения дополнительной информации на [портале Azure можно добавить пользователей совместной работы Azure Active Directory B2B.](../active-directory/b2b/add-users-administrator.md) | Требуется создать группу Azure AD с гостевыми пользователями в качестве участников для настройки этой группы в качестве админ-админ Azure AD для MI. Для получения дополнительной [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)информации см. |

В качестве наилучшей практики для существующих ad-интерфейсов Azure AD для MI, созданных до GA, и все еще работающего после GA, сбросить админ Azure с помощью портала Azure «Удалите админ» и «Установить админ» для того же пользователя или группы AD Azure.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Известные проблемы с журналом Azure AD, входе в GA для MI

- Если в основной базе данных для MI, созданной с помощью `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`команды T-S'L, существует логин Azure AD, он не может быть настроен как админ Azure AD для MI. Вы обнаружите ошибку, установив логин в качестве админа Azure AD с помощью портала Azure, PowerShell или CLI-команд для создания входа в Azure AD.
  - Логин должен быть удален в основной `DROP LOGIN [myaadaccount]`базе данных с помощью команды, прежде чем учетная запись может быть создана как админ AD Azure.
  - Настройка учетной записи AD Azure на портале `DROP LOGIN` Azure после успеха. 
  - Если вы не можете настроить учетную запись ad-накопителей Azure, зарегистрируйся в основной базе данных управляемого экземпляра для входа. Используйте следующую команду: `SELECT * FROM sys.server_principals`.
  - Настройка admin Azure AD для MI автоматически создаст логин в основной базе данных для этой учетной записи. Удаление админа Azure AD автоматически выбросит логин из основной базы данных.

- Индивидуальные гостевые пользователи Azure AD не поддерживаются в качестве админ-админов Azure AD для MI. Гость-пользователи должны быть частью группы Azure AD, которая будет создана как admin Azure AD. В настоящее время лезвие портала Azure не серый из гостевых пользователей для другого Azure AD, что позволяет пользователям продолжать с настройкой админ. Сохранение гостевых пользователей в качестве админа Azure AD приведет к сбою в настройке.
  - Если вы хотите сделать гостем админ AD Azure AD для MI, включите гостевого пользователя в группу Azure AD и установите эту группу в качестве админа Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell для управляемого экземпляра S'L

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Модуль AzureRM будет получать исправления ошибок по крайней мере до декабря 2020 года.  Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны. Подробнее об их совместимости читайте [в новом модуле Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- Выберите-AzSubscription

Cmdlets, используемые для предоставления и управления админ-амин Azure AD для управляемого экземпляра S'L:

| Имя командлета | Описание |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryАдминистратор](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Предусматривает администраторAzре AD для управляемого экземпляра S'L в текущей подписке. (Должно быть от текущей подписки)|
| [Удалить-AzSqlInstanceActiveDirectoryАдминистратор](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Удаляет администратор Azure AD для управляемого экземпляра S'L в текущей подписке. |
| [Get-AzSqlInstanceActiveDirectoryАдминистратор](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Возвращает информацию об администраторе Azure AD для управляемого экземпляра S'L в текущей подписке.|

Следующая команда получает информацию об администраторе Azure AD для управляемого экземпляра под названием ManagedInstance01, который связан с группой ресурсов под названием ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Следующая команда содержит положения группы администраторов Azure AD под названием DBA для управляемого экземпляра под названием ManagedInstance01. Этот сервер связан с ресурсной группой ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Следующая команда удаляет администратор Azure AD для управляемого экземпляра под названием ManagedInstanceName01, связанного с группой ресурсов ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Вы также можете предоставить админ AD Azure для экземпляра, управляемого S'L, позвонив по следующим командам CLI:

| Команда | Описание |
| --- | --- |
|[az sql mi ad-admin создать](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Предусматривает администратора active-каталога Azure для управляемого экземпляра S'L. (Должно быть от текущей подписки) |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Удаляет администратор аниме Active Directory Azure для управляемого экземпляра S'L. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Возвращает информацию об администраторе active-каталога Azure, который в настоящее время настроен для управляемого экземпляра S'L. |
|[az sql mi объявление-админ обновление](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Обновление администратора Active Directory для управляемого экземпляра S'L. |

Для получения дополнительной информации о [az sql mi](/cli/azure/sql/mi)командах CLI см.

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Подготовка администратора Azure Active Directory для сервера Базы данных SQL Azure

> [!IMPORTANT]
> Выполните следующие действия, только если вы подготавливаете сервер Базы данных SQL Azure или хранилище данных Azure.

Ниже описаны две процедуры по подготовке администратора Azure Active Directory для сервера Azure SQL Server на портале Azure или с помощью PowerShell.

### <a name="azure-portal"></a>Портал Azure

1. В правом верхнем углу [портала Azure](https://portal.azure.com/) щелкните имя подключения, чтобы открыть список доступных каталогов Active Directory. Выберите нужный экземпляр Active Directory в качестве Azure AD по умолчанию. На этом шаге связанный с подпиской каталог Active Directory связывается с Azure SQL Server, чтобы одна и та же подписка использовалась для Azure AD и SQL Server. (На сервере Azure SQL Server может размещаться база данных SQL Azure или хранилище данных SQL Azure.)

    ![choose-ad][8]

2. Поиск и выберите **сервер S'L.**

    ![Поиск и выбор серверов S'L](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > На этой странице, прежде чем выбрать **Серверы SQL Server**, можно щелкнуть **звезду** рядом с именем, чтобы добавить категорию в *Избранное* и добавить **серверы SQL Server** на левую панель навигации.

3. На странице **SQL Server** выберите **Администратор Active Directory**.

4. На странице **Администратор Active Directory** щелкните **Задать администратора**.

    ![Серверы S'L устанавливают сятвую димин-каталог](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. На странице **администратора добавить** ищите пользователя, выберите пользователя или группу администратора, а затем **выберите «** На странице "Администратор Active Directory" отобразятся все участники и группы Active Directory. Пользователей или группы, которые выделены серым цветом, нельзя выбрать; они не поддерживаются ролью администратора Azure AD. (См. список поддерживаемых администрирований в разделе **Функции и ограничения Azure AD** и раздела [«Активная проверка подлинности каталогов Azure» для проверки подлинности с помощью базы данных СЗЛ или хранилища данных S'L.)](sql-database-aad-authentication.md) Элемент управления доступом на основе ролей (RBAC) применяется только к порталу и не распространяется на сервер S'L Server.

    ![Выберите admin-каталог Active](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. В верхней части страницы **ремин -Тёпового каталога** выберите **SAVE**.

    ![сохранение администратора](./media/sql-database-aad-authentication/save-admin.png)

Изменение администратора может занять несколько минут. После этого новый администратор появится в поле **Администратор Active Directory** .

   > [!NOTE]
   > При настройке администратора Azure AD новое имя администратора (пользователя или группы) не может уже присутствовать в виртуальной базе данных master как пользователь аутентификации SQL Server. Если оно присутствует, настройка администратора Azure AD завершится ошибкой. Будет выполнен откат его создания и появится сообщение, что такой администратор (имя) уже существует. Такой пользователь аутентификации SQL Server не входит в Azure AD, поэтому любая попытка подключиться к серверу с помощью аутентификации Azure AD завершится сбоем.

Чтобы потом удалить учетную запись администратора, в верхней части страницы **Администратор Active Directory** щелкните **Удалить администратора**, а затем нажмите кнопку **Сохранить**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell для базы данных Azure S'L и склада данных Azure S'L

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). Чтобы подготовить администратора Azure AD, выполните следующие команды Azure PowerShell.

- Connect-AzAccount
- Выберите-AzSubscription

Cmdlets используется для предоставления и управления админ-администрированием Azure AD для базы данных Azure S'L и хранилища данных Azure S'L:

| Имя командлета | Описание |
| --- | --- |
| [Set-AzSqlServerActiveDirectorDirectoryАдминистратор](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Должно быть от текущей подписки) |
| [Удалить-AzSqlServerActiveDirectordirectoryАдминистратор](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
| [Get-AzSqlServerActiveDirectorDirectoryАдминистратор](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |

Используйте powerShell команды получить-помощь, чтобы увидеть больше информации для каждой из этих команд. Например, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

В следующем сценарии администратор Azure AD **назвал** DBA_Group (object `40b79501-b343-44ed-9ce7-da4c8cc7353f`ID) для **demo_server** сервера в группе ресурсов под названием **Group-23:**

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

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Вы можете предоставить admin Azure AD, позвонив по следующим командам CLI:

| Команда | Описание |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Выполняет подготовку учетной записи администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. (Должно быть от текущей подписки) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Удаляет учетную запись администратора Azure Active Directory для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Возвращает сведения о текущем администраторе Azure Active Directory, настроенном для сервера Azure SQL Server или хранилища данных SQL Azure. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Обновляет учетную запись администратора Active Directory для сервера SQL Server Azure или хранилища данных SQL Azure. |

Для получения дополнительной информации о [az sql server](/cli/azure/sql/server)командах CLI см.

* * *

> [!NOTE]
> Кроме того, можно подготовить администратора Azure Active Directory с помощью интерфейсов REST API. Дополнительные сведения см. в справочнике по REST API управления службами и статье [Operations for Azure SQL Databases](/rest/api/sql/) (Операции для Базы данных SQL Azure).

## <a name="configure-your-client-computers"></a>Настройка клиентских компьютеров

Приложения или пользователи подключаются к Базе данных SQL Azure или хранилищу данных SQL, используя удостоверения Azure AD, с клиентских компьютеров. На эти компьютеры необходимо установить указанное ниже программное обеспечение.

- .NET Framework 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)или позже от .
- Библиотека аутентификации активных каталогов Azure для сервера S'L *(ADAL. DLL*). Ниже приведены ссылки на загрузку для установки последних SSMS, ODBC и OLE DB драйвер, который содержит *ADAL. Библиотека DLL.*
    1. [Среда SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver for SQL Server версии 17](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [ВОДИТЕЛЬ OLE DB 18 для сервера S'L](https://www.microsoft.com/download/details.aspx?id=56730)

Вы можете выполнить эти требования, сделав следующее:

- Установка последней версии [студии управления серверами s'L](/sql/ssms/download-sql-server-management-studio-ssms) или [инструментов данных серверов S'L](/sql/ssdt/download-sql-server-data-tools-ssdt) соответствует требованиям .NET Framework 4.6.
    - SSMS устанавливает версию x86 *ADAL. DLL*.
    - SSDT устанавливает версию amd64 *ADAL. DLL*.
    - Последняя визуальная студия от [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) соответствует требованиям .NET Framework 4.6, но не устанавливает требуемую версию amd64 *ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Создание пользователей автономной базы данных в базе данных, сопоставленной с удостоверениями Azure AD

> [!IMPORTANT]
> Управляемый экземпляр теперь поддерживает принципы серверов Azure AD (логины), что позволяет создавать логины из пользователей, групп или приложений Azure AD. Принципы серверов AD Azure AD (логины) обеспечивают возможность аутентификации управляемого экземпляра, не требуя создания пользователей баз данных в качестве пользователя, содержащегося в базе данных. Для получения дополнительной информации [см.](sql-database-managed-instance.md#azure-active-directory-integration) Сведения о синтаксисе для создания субъектов сервера (имен для входа) Azure AD см. в статье <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN (Transact-SQL)</a>.

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
> Эта команда требует, чтобы от имени зарегистрированного пользователя S'L получил доступ к Azure AD ("внешний поставщик"). Иногда возникают обстоятельства, которые заставляют Azure AD вернуть исключение обратно в S'L. В этих случаях пользователь увидит ошибку 33134, которая должна содержать сообщение об ошибке AAD. В большинстве случаев ошибка говорит, что доступ отказано, или что пользователь должен зарегистрироваться в МИД для доступа к ресурсу, или что доступ между приложениями первой стороны должны быть обработаны с помощью предварительной авторизации. В первых двух случаях проблема обычно возникает из-за политики условного доступа, установленные в нанимателем AAD пользователя: они мешают пользователю получить доступ к внешнему поставщику. Обновление политики CA, чтобы разрешить доступ к приложению '00000002-0000-0000-c000-00000000000000000' (идентификатор приложения AAD Graph API) должен решить эту проблему. В случае, если ошибка говорит о том, что доступ между приложениями первого участника должен быть обработан с помощью предварительной авторизации, проблема заключается в том, что пользователь вписывается в систему в качестве основного обслуживания. Команда должна быть успешной, если она выполняется пользователем.

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

2. В поле **имени пользователя** введите имя пользователя Azure Active Directory в названии **\@пользователя формата domain.com.** Именем пользователя должна быть учетная запись из Azure Active Directory или учетная запись из федерации домена с Azure Active Directory.

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

1. Зарегистрируйте приложение в каталоге Azure Active Directory и получите идентификатор клиента для вашего кода.
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
> `sqlcmd`с `-G` командой не работает с идентификаторами системы, и требуется основной логин пользователя.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Проверка подлинности Azure AD

Руководство по устранению неполадок с Azure AD Authentication можно найти в следующем блоге:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Дальнейшие действия

- Для получения обзора логинов, пользователей, ролей баз данных и разрешений в базе данных S'L [см. Логины, пользователи, роли в базе данных и учетные записи пользователей.](sql-database-manage-logins.md)
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
