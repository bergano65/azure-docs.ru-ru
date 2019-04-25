---
title: Доменные службы Azure Active Directory. Синхронизация определенных учетных записей | Документация Майкрософт
description: Настройка синхронизации определенных учетных записей из Azure AD с управляемыми доменами
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: ac11244b87c87285722b4922da69530fab98c299
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416489"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Настройка синхронизации определенных учетных записей из Azure AD с управляемым доменом
В этой статье показано, как настроить синхронизацию учетных записей только определенных пользователей из каталога Azure AD с управляемым доменом доменных служб Azure AD.


## <a name="group-based-scoped-synchronization"></a>Синхронизация определенных учетных записей на основе группы
По умолчанию все пользователи и группы в каталоге Azure AD синхронизированы с управляемым доменом. Если управляемый домен используют всего несколько пользователей, вы можете синхронизировать учетные записи только этих пользователей. Синхронизация определенных учетных записей на основе группы позволяет сделать это. Если ее настроить, с управляемым доменом синхронизируются только учетные записи пользователей, принадлежащих к указанным группам.

Приведенная ниже таблица поможет определить, как использовать синхронизацию определенных учетных записей:

| **Текущее состояние** | **Требуемое состояние** | **Требуемая конфигурация** |
| --- | --- | --- |
| В имеющемся управляемом домене настроена синхронизация учетных записей всех пользователей и групп. | Вы хотите синхронизировать с управляемым доменом учетные записи только тех пользователей, которые принадлежат к определенным группам. | [Удалите имеющийся управляемый домен](active-directory-ds-disable-aadds.md), а затем следуйте инструкциям в этой статье, чтобы повторно создать его с настроенной синхронизацией определенных учетных записей. |
| У вас нет управляемого домена. | Вы хотите создать управляемый домен и синхронизировать учетные записи только тех пользователей, которые принадлежат к определенным группам. | Следуйте инструкциям в этой статье, чтобы создать управляемый домен с настроенной синхронизацией определенных учетных записей. |
| В имеющемся управляемом домене настроена синхронизация только тех учетных записей, которые принадлежат к определенным группам. | Вы хотите внести изменения в список групп, пользователей которых необходимо синхронизировать с управляемым доменом. | Следуйте инструкциям в этой статье, чтобы изменить синхронизацию определенных учетных записей. |

> [!WARNING]
> **Изменение области синхронизации приводит к тому, что управляемый домен проходит через повторную синхронизацию.**
> 
>  * При изменении области синхронизации для управляемого домена происходит полная повторная синхронизация.
>  * Объекты, которые больше не нужны в управляемом домене, будут удалены. В управляемом домене создаются новые объекты.
>  * Повторная синхронизация может занять много времени в зависимости от количества объектов (пользователи, группы и членства в группах) в управляемом домене и каталоге Azure AD. В больших каталогах с сотнями тысяч объектов повторная синхронизация может занять несколько дней.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Создание управляемого домена и включение синхронизации определенных учетных записей на основе группы с помощью портала Azure

1. Следуйте шагам, описанным в руководстве по [началу работы](active-directory-ds-getting-started.md), чтобы создать управляемый домен.
2. В мастере создания доменных служб Azure AD выберите **С заданной областью** для способа синхронизации.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Создание управляемого домена и включение синхронизации определенных учетных записей на основе группы с помощью PowerShell
Выполните эти шаги с помощью PowerShell. Чтобы включить доменные службы Azure Active Directory с помощью PowerShell, [обратитесь к инструкциям](active-directory-ds-enable-using-powershell.md). Несколько шагов в этой статье немного изменены для настройки синхронизации определенных учетных записей.

Чтобы настроить синхронизацию определенных учетных записей на основе группы с управляемым доменом, сделайте следующее:

1. Выполните следующие задачи:
   * [Задача 1. Установка необходимых модулей PowerShell](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
   * [Задача 2. Создание обязательного субъекта-службы в каталоге Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
   * [Задача 3. Создание и настройка группы "Администраторы контроллера домена AAD"](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
   * [Задача 4. Регистрация поставщика ресурсов доменных служб Azure AD](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Задача 5. Создание группы ресурсов](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
   * [Задача 6. Создание и настройка виртуальной сети](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Выберите группы, которые необходимо синхронизировать, и укажите отображаемое имя групп, которые необходимо синхронизировать с управляемым доменом.

3. Сохраните [скрипт в следующем разделе](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) в файл с именем ```Select-GroupsToSync.ps1```. Выполните скрипт, как показано ниже:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Не забудьте добавить группу "Администраторы контроллера домена AAD" в список.**
   >
   > Необходимо добавить группу "Администраторы контроллера домена AAD" в список групп, для которых настроена синхронизация определенных учетных записей. Если эта группа не будет добавлена, управляемый домен станет непригодным для использования.
   >

4. Теперь создайте управляемый домен и включите синхронизацию определенных учетных записей на основе группы для управляемого домена. Добавьте свойство ```"filteredSync" = "Enabled"``` в параметр ```Properties```. Например, см. следующий фрагмент сценария, скопированный из раздела [Задача 7. Подготовка управляемого домена доменных служб Azure AD](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Не забудьте добавить ```"filteredSync" = "Enabled"``` в параметр ```-Properties```, чтобы синхронизация определенных учетных записей была включена для управляемого домена.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Создайте скрипт, чтобы выбрать группы для синхронизации с управляемым доменом (Select-GroupsToSync.ps1)
Сохраните следующий скрипт в файл ```Select-GroupsToSync.ps1```. Этот скрипт настраивает в доменных службах Azure AD синхронизацию выбранных групп с управляемым доменом. Учетные записи всех пользователей, принадлежащих к указанным группам, будут синхронизированы с управляемым доменом.

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


## <a name="modify-group-based-scoped-synchronization"></a>Изменение синхронизации определенных учетных записей на основе группы
Чтобы изменить список групп, пользователи которых должны быть синхронизированы с управляемым доменом, повторно запустите [скрипт PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) и укажите новый список групп. Не забывайте всегда указывать группу "Администраторы контроллера домена AAD" в этом списке.

> [!WARNING]
> **Не забудьте добавить группу "Администраторы контроллера домена AAD" в список.**
>
> Необходимо добавить группу "Администраторы контроллера домена AAD" в список групп, для которых настроена синхронизация определенных учетных записей. Если эта группа не будет добавлена, управляемый домен станет непригодным для использования.
>


## <a name="disable-group-based-scoped-synchronization"></a>Отключение синхронизации определенных учетных записей на основе группы
Чтобы отключить синхронизацию определенных учетных записей на основе группы с управляемым доменом, используйте следующий скрипт PowerShell:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Дальнейшие действия
* [Синхронизация в управляемом домене доменных служб Azure AD](active-directory-ds-synchronization.md)
* [Включение доменных служб Azure Active Directory с помощью PowerShell](active-directory-ds-enable-using-powershell.md)
