---
title: Синхронизация с заданной областью с помощью PowerShell для доменных служб Azure AD | Документация Майкрософт
description: Узнайте, как использовать Azure AD PowerShell для настройки синхронизации с заданной областью из Azure AD в управляемый домен доменных служб Azure Active Directory.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.openlocfilehash: 197ae37b0c63b19ebe4dcdf2732169be0f357a07
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294484"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Настройка синхронизации с заданной областью из Azure AD в Azure Active Directory доменных служб с помощью Azure AD PowerShell

Чтобы предоставить службы проверки подлинности, Azure Active Directory доменные службы (Azure AD DS) синхронизирует пользователей и группы из Azure AD. В гибридной среде пользователи и группы из локальной среды домен Active Directory служб (AD DS) можно сначала синхронизировать с Azure AD с помощью Azure AD Connect, а затем синхронизировать с Azure AD DS.

По умолчанию все пользователи и группы из каталога Azure AD синхронизируются с управляемым доменом AD DS Azure. При наличии конкретных потребностей можно выбрать синхронизацию только определенного набора пользователей.

В этой статье показано, как создать управляемый домен, использующий синхронизацию с заданной областью, а затем изменить или отключить набор пользователей с областью действия с помощью Azure AD PowerShell. [Эти действия также можно выполнить с помощью портал Azure][scoped-sync].

## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости выполните инструкции из руководства по [созданию и настройке управляемого домена доменных служб Azure Active Directory][tutorial-create-instance].
* Для изменения области синхронизации AD DS Azure требуются права *глобального администратора* в клиенте Azure AD.

## <a name="scoped-synchronization-overview"></a>Общие сведения о синхронизации с заданной областью

По умолчанию все пользователи и группы из каталога Azure AD синхронизируются с управляемым доменом. Если доступ к управляемому домену требуется только некоторым пользователям, можно синхронизировать только эти учетные записи пользователей. Эта синхронизация с областью действия основана на группе. При настройке синхронизации с областью действия на уровне группы только учетные записи пользователей, принадлежащие к указанным вами группам, синхронизируются с управляемым доменом. Вложенные группы не синхронизируются, только выбранные группы.

Область синхронизации можно изменить при создании управляемого домена или после его развертывания. Теперь можно также изменить область синхронизации в существующем управляемом домене, не требуя его повторного создания.

Дополнительные сведения о процессе синхронизации см. в разделе [Общие сведения о синхронизации в доменных службах Azure AD][concepts-sync].

> [!WARNING]
> Изменение области синхронизации приводит к повторной синхронизации всех данных управляемым доменом. Действительны следующие условия.
>
>  * При изменении области синхронизации для управляемого домена происходит полная повторная синхронизация.
>  * Объекты, которые больше не требуются в управляемом домене, удаляются. В управляемом домене создаются новые объекты.

## <a name="powershell-script-for-scoped-synchronization"></a>Скрипт PowerShell для синхронизации с заданной областью

Чтобы настроить синхронизацию с заданной областью с помощью PowerShell, сначала сохраните следующий скрипт в файл с именем `Select-GroupsToSync.ps1` .

Этот сценарий настраивает AD DS Azure для синхронизации выбранных групп из Azure AD. Все учетные записи пользователей, которые являются частью указанных групп, синхронизируются с управляемым доменом.

Этот сценарий используется в дополнительных шагах в этой статье.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Включить синхронизацию с заданной областью

Чтобы включить синхронизацию с областью действия на уровне группы для управляемого домена, выполните следующие действия.

1. Сначала задайте *"филтередсинк" = "Enabled* " в ресурсе Azure AD DS, а затем обновите управляемый домен.

    При появлении запроса укажите учетные данные *глобального администратора* для входа в клиент Azure AD с помощью командлета [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Теперь укажите список групп, чьи пользователи должны синхронизироваться с управляемым доменом.

    Запустите `Select-GroupsToSync.ps1` Скрипт и укажите список групп для синхронизации. В следующем примере группы для синхронизации являются *GroupName1* и *GroupName2*.

    > [!WARNING]
    > Группу *администраторов контроллера домена AAD* необходимо включить в список групп для синхронизации с заданной областью. Если эта группа не включена, управляемый домен будет непригоден для использования.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Изменение области синхронизации приводит к повторной синхронизации всех данных управляемым доменом. Объекты, которые больше не требуются в управляемом домене, удаляются, и повторная синхронизация может занять длительное время.

## <a name="modify-scoped-synchronization"></a>Изменение синхронизации с заданной областью

Чтобы изменить список групп, пользователей которых нужно синхронизировать с управляемым доменом, выполните `Select-GroupsToSync.ps1` сценарий и укажите новый список групп для синхронизации.

В следующем примере группы для синхронизации больше не включают *GroupName2*и теперь включают *GroupName3*.

> [!WARNING]
> Группу *администраторов контроллера домена AAD* необходимо включить в список групп для синхронизации с заданной областью. Если эта группа не включена, управляемый домен будет непригоден для использования.

При появлении запроса укажите учетные данные *глобального администратора* для входа в клиент Azure AD с помощью командлета [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Изменение области синхронизации приводит к повторной синхронизации всех данных управляемым доменом. Объекты, которые больше не требуются в управляемом домене, удаляются, и повторная синхронизация может занять длительное время.

## <a name="disable-scoped-synchronization"></a>Отключить синхронизацию с заданной областью

Чтобы отключить синхронизацию с областью действия группы для управляемого домена, установите *"филтередсинк" = "Disabled"* в ресурсе Azure AD DS, а затем обновите управляемый домен. По завершении настройки для всех пользователей и групп будет настроена синхронизация из Azure AD.

При появлении запроса укажите учетные данные *глобального администратора* для входа в клиент Azure AD с помощью командлета [Connect-AzureAD][Connect-AzureAD] :

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Изменение области синхронизации приводит к повторной синхронизации всех данных управляемым доменом. Объекты, которые больше не требуются в управляемом домене, удаляются, и повторная синхронизация может занять длительное время.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о процессе синхронизации см. в разделе [Общие сведения о синхронизации в доменных службах Azure AD](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
