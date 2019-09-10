---
title: Синхронизация с заданной областью для доменных служб Azure AD | Документация Майкрософт
description: Узнайте, как настроить синхронизацию с заданной областью из Azure AD в управляемый домен доменных служб Azure Active Directory.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842679"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Настройка синхронизации с заданной областью из Azure AD в Azure Active Directory доменных служб

Чтобы предоставить службы проверки подлинности, Azure Active Directory доменные службы (Azure AD DS) синхронизирует пользователей и группы из Azure AD. В гибридной среде пользователи и группы из локальной среды домен Active Directory служб (AD DS) можно сначала синхронизировать с Azure AD с помощью Azure AD Connect, а затем синхронизировать с Azure AD DS. По умолчанию все пользователи и группы из каталога Azure AD синхронизируются с управляемым доменом AD DS Azure. При наличии конкретных потребностей можно выбрать синхронизацию только определенного набора пользователей.

В этой статье показано, как создать управляемый домен AD DS Azure, использующий синхронизацию с заданной областью, а затем изменить или отключить набор пользователей с областью действия.

## <a name="scoped-synchronization-overview"></a>Общие сведения о синхронизации с заданной областью

По умолчанию все пользователи и группы из каталога Azure AD синхронизируются с управляемым доменом AD DS Azure. Если доступ к управляемому домену требуется только некоторым пользователям, можно синхронизировать только эти учетные записи пользователей. Эта синхронизация с областью действия основана на группе. При настройке синхронизации с областью действия на уровне группы только учетные записи пользователей, принадлежащие к указанным вами группам, синхронизируются с управляемым доменом AD DS Azure.

В следующей таблице показано, как использовать синхронизацию с заданной областью.

| Текущее состояние | Требуемое состояние | Требуемая конфигурация |
| --- | --- | --- |
| Существующий управляемый домен настроен для синхронизации всех учетных записей и групп пользователей. | Необходимо синхронизировать только учетные записи пользователей, принадлежащие к конкретным группам. | Вы не можете изменить синхронизацию всех пользователей для использования синхронизации с заданной областью. [Удалите существующий управляемый домен](delete-aadds.md), а затем выполните действия, описанные в этой статье, чтобы повторно создать управляемый домен Azure AD DS с настроенной синхронизацией с заданной областью. |
| Нет существующего управляемого домена. | Вы хотите создать управляемый домен и синхронизировать учетные записи только тех пользователей, которые принадлежат к определенным группам. | Выполните действия, описанные в этой статье, чтобы создать управляемый домен Azure AD DS с настроенной синхронизацией с заданной областью. |
| Существующий управляемый домен настраивается для синхронизации только учетных записей, принадлежащих к конкретным группам. | Необходимо изменить список групп, пользователи которых должны синхронизироваться с управляемым доменом AD DS Azure. | Выполните действия, описанные в этой статье, чтобы изменить синхронизацию с заданной областью. |

Для настройки параметров синхронизации с областью действия используется портал Azure или PowerShell:

| Action | | |
|--|--|--|
| Создание управляемого домена Azure AD DS и Настройка синхронизации с заданной областью | [портал Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Изменение синхронизации с заданной областью | [портал Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Отключить синхронизацию с заданной областью | [портал Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Изменение области синхронизации приводит к тому, что управляемый домен Azure AD DS будет повторно синхронизировать все данные.
> 
>  * При изменении области синхронизации для управляемого домена Azure AD DS выполняется полная повторная синхронизация.
>  * Объекты, которые больше не требуются в управляемом домене Azure AD DS, удаляются. В управляемом домене создаются новые объекты.
>  * Повторная синхронизация может занять длительное время. Время синхронизации зависит от числа объектов, таких как пользователи, группы и членство в группах, в управляемом домене Azure AD DS и каталоге Azure AD. В больших каталогах с сотнями тысяч объектов повторная синхронизация может занять несколько дней.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Включение синхронизации с заданной областью с помощью портал Azure

1. Следуйте указаниям [руководства, чтобы создать и настроить экземпляр AD DS Azure](tutorial-create-instance.md). Выполните все необходимые условия и шаги развертывания, кроме области синхронизации.
1. Выберите **область** на шаге синхронизации, а затем выберите группы Azure AD для синхронизации с экземпляром AD DS Azure.

Для завершения развертывания управляемый домен AD DS Azure может занять до часа. В портал Azure на странице **Обзор** управляемого домена AD DS Azure отображается текущее состояние на протяжении этого этапа развертывания.

Когда портал Azure показывает, что управляемый домен AD DS Azure завершил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите управляемый домен Azure AD DS на портале. В окне **Обзор** появится запрос на автоматическую настройку этих параметров DNS.
* [Включите синхронизацию паролей с доменными службами Azure AD](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , чтобы конечные пользователи могли входить в управляемый домен с использованием своих корпоративных учетных данных.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Измените синхронизацию с областью с помощью портал Azure

Чтобы изменить список групп, пользователей которых нужно синхронизировать с управляемым доменом AD DS Azure, выполните следующие действия.

1. В портал Azure выберите экземпляр AD DS Azure, например *contoso.com*.
1. Выберите **Синхронизация** в меню в левой части.
1. Чтобы добавить группу, в верхней части щелкните **+ выбрать группы** , а затем выберите группы для добавления.
1. Чтобы удалить группу из области синхронизации, выберите ее из списка синхронизированных групп и нажмите кнопку **удалить группы**.
1. После внесения всех изменений выберите **Сохранить область синхронизации**.

Изменение области синхронизации приводит к тому, что управляемый домен Azure AD DS будет повторно синхронизировать все данные. Объекты, которые больше не требуются в управляемом домене Azure AD DS, удаляются, и повторная синхронизация может занять длительное время.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Отключение синхронизации с заданной областью с помощью портал Azure

Чтобы отключить синхронизацию с областью действия группы для управляемого домена Azure AD DS, выполните следующие действия.

1. В портал Azure выберите экземпляр AD DS Azure, например *contoso.com*.
1. Выберите **Синхронизация** в меню в левой части.
1. Задайте **для области синхронизации значение** **все**, а затем выберите **Сохранить область синхронизации**.

Изменение области синхронизации приводит к тому, что управляемый домен Azure AD DS будет повторно синхронизировать все данные. Объекты, которые больше не требуются в управляемом домене Azure AD DS, удаляются, и повторная синхронизация может занять длительное время.

## <a name="powershell-script-for-scoped-synchronization"></a>Скрипт PowerShell для синхронизации с заданной областью

Чтобы настроить синхронизацию с заданной областью с помощью PowerShell, сначала сохраните следующий скрипт `Select-GroupsToSync.ps1`в файл с именем. Этот сценарий настраивает AD DS Azure для синхронизации выбранных групп из Azure AD. Все учетные записи пользователей, входящие в указанные группы, синхронизируются с управляемым доменом AD DS Azure.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Включение синхронизации с заданной областью с помощью PowerShell

Выполните эти шаги с помощью PowerShell. Чтобы включить доменные службы Azure Active Directory с помощью PowerShell, [обратитесь к инструкциям](powershell-create-instance.md). Несколько шагов в этой статье немного изменены для настройки синхронизации определенных учетных записей.

1. Выполните следующие задачи из статьи, чтобы включить AD DS Azure с помощью PowerShell. Чтобы на самом деле создать управляемый домен, завершите работу на шаге. Вы настраиваете синхронизацию с заданной областью, чтобы создать управляемый домен AD DS Azure.

   * [Установите необходимые модули PowerShell](powershell-create-instance.md#prerequisites).
   * [Создайте требуемый субъект-службу и группу Azure AD для административного доступа](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Создавайте вспомогательные ресурсы Azure, такие как виртуальная сеть и подсети](powershell-create-instance.md#create-supporting-azure-resources).

1. Определите группы и пользователей, которые они содержат, которые вы хотите синхронизировать из Azure AD. Создайте список отображаемых имен групп для синхронизации с AD DS Azure.

1. Запустите [скрипт из предыдущего раздела](#powershell-script-for-scoped-synchronization) и используйте параметр *-граупстоадд* , чтобы передать список групп для синхронизации.

   > [!WARNING]
   > Группу *администраторов контроллера домена AAD* необходимо включить в список групп для синхронизации с заданной областью. Если вы не включите эту группу, управляемый домен AD DS Azure будет непригоден для использования.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Теперь создайте управляемый домен Azure AD DS и включите синхронизацию с областью действия на уровне группы. Включите *"филтередсинк" = "Enabled"* в параметр *-Properties* .

    Задайте идентификатор подписки Azure, а затем укажите имя управляемого домена, например *contoso.com*. Идентификатор подписки можно получить с помощью командлета [Get-азсубскриптион][Get-AzSubscription] . Задайте для имени группы ресурсов, имени виртуальной сети и региона значения, которые использовались на предыдущих шагах для создания вспомогательных ресурсов Azure.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Создание ресурса и возврат управления в командную строку PowerShell занимает несколько минут. Управляемый домен AD DS Azure по всей важности подготавливается в фоновом режиме и может занять до часа для завершения развертывания. В портал Azure на странице **Обзор** управляемого домена AD DS Azure отображается текущее состояние на протяжении этого этапа развертывания.

Когда портал Azure показывает, что управляемый домен AD DS Azure завершил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите управляемый домен Azure AD DS на портале. В окне **Обзор** появится запрос на автоматическую настройку этих параметров DNS.
* [Включите синхронизацию паролей с доменными службами Azure AD](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , чтобы конечные пользователи могли входить в управляемый домен с использованием своих корпоративных учетных данных.

## <a name="modify-scoped-synchronization-using-powershell"></a>Изменение ограниченной синхронизации с помощью PowerShell

Чтобы изменить список групп, пользователей которых нужно синхронизировать с управляемым доменом AD DS Azure, повторно запустите [сценарий PowerShell](#powershell-script-for-scoped-synchronization) и укажите новый список групп. В следующем примере группы для синхронизации больше не включают *GroupName2*и теперь включают *GroupName3*.

> [!WARNING]
> Группу *администраторов контроллера домена AAD* необходимо включить в список групп для синхронизации с заданной областью. Если вы не включите эту группу, управляемый домен AD DS Azure будет непригоден для использования.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Изменение области синхронизации приводит к тому, что управляемый домен Azure AD DS будет повторно синхронизировать все данные. Объекты, которые больше не требуются в управляемом домене Azure AD DS, удаляются, и повторная синхронизация может занять длительное время.

## <a name="disable-scoped-synchronization-using-powershell"></a>Отключение синхронизации с заданной областью с помощью PowerShell

Чтобы отключить синхронизацию с областью действия группы для управляемого домена Azure AD DS, установите в ресурсе Azure AD DS *"филтередсинк" = "Disabled"* , а затем обновите управляемый домен. По завершении настройки для всех пользователей и групп будет настроена синхронизация из Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Изменение области синхронизации приводит к тому, что управляемый домен Azure AD DS будет повторно синхронизировать все данные. Объекты, которые больше не требуются в управляемом домене Azure AD DS, удаляются, и повторная синхронизация может занять длительное время.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о процессе синхронизации см. в разделе [Общие сведения о синхронизации в доменных службах Azure AD](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
