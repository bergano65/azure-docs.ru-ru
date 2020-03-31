---
title: Скопированная синхронизация для служб доменов Azure AD (ru) Документы Майкрософт
description: Узнайте, как настроить синхронизацию с Azure AD на домен с активным каталогом Azure Active Directory Domain
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613042"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Настройка синхронизации с Azure AD на службы активных каталогов Azure

Для обеспечения служб аутентификации службы azure Active Directory Domain Domain Services (Azure AD DS) синхронизируют пользователей и группы из Azure AD. В гибридной среде пользователи и группы из предварительной среды Active Directory Domain Services (AD DS) могут сначала синхронизироваться с Azure AD с помощью Azure AD Connect, а затем синхронизировать сяслины в Azure AD DS.

По умолчанию все пользователи и группы из каталога Azure AD синхронизированы с доменом Azure AD DS. Если у вас есть конкретные потребности, вы можете синхронизировать только определенный набор пользователей.

В этой статье показано, как создать управляемый dS Azure AD, который использует синхронизацию, а затем изменяет или отменяет набор пользователей.

## <a name="scoped-synchronization-overview"></a>Обзор скопированных синхронизаций

По умолчанию все пользователи и группы из каталога Azure AD синхронизированы с доменом Azure AD DS. Если только несколько пользователей должны получить доступ к управляемому домену, вы можете синхронизировать только эти учетные записи пользователей. Эта синхронизация является групповой. При настройке синхронизации с областью охвата группы синхронизация синхронизируется только учетные записи пользователей, принадлежащие к указанным группам, синхронизируются с доменом Azure AD DS.

В следующей таблице описано, как использовать синхронизацию с использованием сферы охвата:

| Текущее состояние | Требуемое состояние | Требуемая конфигурация |
| --- | --- | --- |
| Существующий управляемый домен настроен для синхронизации всех учетных записей пользователей и групп. | Необходимо синхронизировать только учетные записи пользователей, принадлежащие к определенным группам. | Нельзя переходить от синхронизации всех пользователей к использованию синхронизации с охватом. [Удалите существующий управляемый домен,](delete-aadds.md)затем выполните последующие шаги в этой статье, чтобы воссоздать управляемый домен Azure AD DS с настраиваемым синхронизацией. |
| Нет существующего управляемого домена. | Вы хотите создать управляемый домен и синхронизировать учетные записи только тех пользователей, которые принадлежат к определенным группам. | Выполните действия в этой статье, чтобы создать домен СУ AD СAzure с настройкой синхронизации. |
| Существующий управляемый домен настроен для синхронизации только учетных записей, принадлежащих определенным группам. | Необходимо изменить список групп, пользователи которых должны быть синхронизированы с доменом Azure AD DS. | Выполните действия в этой статье, чтобы изменить синхронизацию. |

Для настройки параметров синхронизации прим.

| Действие | | |
|--|--|--|
| Создание домена Azure AD DS, управляемого DS, и настройка синхронизации с охватом | [Портал Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Изменение синхронизации сферы охвата | [Портал Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Отключить синхронизацию | [Портал Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Изменение сферы синхронизации приводит к тому, что управляемый домен Azure AD DS пересинхронизирует все данные.
> 
>  * При изменении области синхронизации для домена Azure AD DS происходит полная ресинхронизация.
>  * Объекты, которые больше не требуются в домене Azure AD DS, удаляются. В управляемом домене создаются новые объекты.
>  * Ресинхронизация может занять много времени. Время синхронизации зависит от количества объектов, таких как пользователи, группы и членство в группе в домене Azure AD DS и каталоге Azure AD. В больших каталогах с сотнями тысяч объектов повторная синхронизация может занять несколько дней.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Включить синхронизацию с помощью портала Azure

1. Следуйте [учебнику, чтобы создать и настроить экземпляр Azure AD DS.](tutorial-create-instance-advanced.md) Выполните все предпосылки и шаги развертывания, кроме области синхронизации.
1. Выберите **Scoped** на этапе синхронизации, а затем выберите группы Azure AD для синхронизации с экземпляром Azure AD DS.

Управляемый домен Azure AD DS может занять до часа для завершения развертывания. На портале Azure страница **«Обзор»** для домена Azure AD DS показывает текущее состояние на протяжении всего этапа развертывания.

Когда портал Azure показывает, что управляемый домен Azure AD DS закончил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите на портале управляемый DS AD Azure AD. В окне **Обзора** вам предлагается автоматически настроить настройки DNS.
* [Позволяет синхронизировать пароли в службы домена Azure AD,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) чтобы конечные пользователи могли войти в управляемый домен, используя свои корпоративные учетные данные.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Изменение синхронизации с помощью портала Azure

Чтобы изменить список групп, пользователи которых должны быть синхронизированы с доменом Azure AD DS, выполните следующие шаги:

1. На портале Azure ищите и выбирайте **службы домена Azure AD.** Выберите экземпляр, *например, aaddscontoso.com*.
1. Выберите **синхронизацию** из меню на левой стороне.
1. Чтобы добавить группу, выберите **группы** вверху, а затем выберите группы для добавления.
1. Чтобы удалить группу из области синхронизации, выберите ее из списка синхронизированных групп и выберите **Удалить группы.**
1. При внесении всех изменений выберите **область синхронизации Сохранить.**

Изменение сферы синхронизации приводит к тому, что управляемый домен Azure AD DS пересинхронизирует все данные. Объекты, которые больше не требуются в домене Azure AD DS, удаляются, и ресинхронизация может занять много времени.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Отключить синхронизацию с помощью портала Azure

Чтобы отключить синхронизацию с учетом группдля для управляемого домена Azure AD DS, выполните следующие шаги:

1. На портале Azure ищите и выбирайте **службы домена Azure AD.** Выберите экземпляр, *например, aaddscontoso.com*.
1. Выберите **синхронизацию** из меню на левой стороне.
1. Установите область синхронизации от **Scoped** to **All,** а затем выберите **область синхронизации «Сохранить».**

Изменение сферы синхронизации приводит к тому, что управляемый домен Azure AD DS пересинхронизирует все данные. Объекты, которые больше не требуются в домене Azure AD DS, удаляются, и ресинхронизация может занять много времени.

## <a name="powershell-script-for-scoped-synchronization"></a>Скрипт PowerShell для синхронизации с охватом

Для настройки синхронизации с помощью PowerShell сначала сохраните следующий скрипт в файле с именем. `Select-GroupsToSync.ps1` Этот скрипт настраивает Azure AD DS для синхронизации выбранных групп из Azure AD. Все учетные записи пользователей, входят в указанные группы, синхронизируются с доменом Azure AD DS.

Этот скрипт используется в дополнительных шагах в этой статье.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Включить синхронизацию с помощью PowerShell

Выполните эти шаги с помощью PowerShell. Чтобы включить доменные службы Azure Active Directory с помощью PowerShell, [обратитесь к инструкциям](powershell-create-instance.md). Несколько шагов в этой статье немного изменены для настройки синхронизации определенных учетных записей.

1. Выполните следующие задачи из статьи, чтобы включить Azure AD DS с помощью PowerShell. Остановитесь на шаг, чтобы фактически создать управляемый домен. Настраиваете синхронизацию с охватом, создавая домена Azure AD DS.

   * [Установите необходимые модули PowerShell.](powershell-create-instance.md#prerequisites)
   * [Создайте требуемую основную службу и группу Azure AD для административного доступа.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [Создавайте вспомогательные ресурсы Azure, такие как виртуальная сеть и подсети.](powershell-create-instance.md#create-supporting-azure-resources)

1. Определите группы и пользователей, которые они содержат, которые необходимо синхронизировать с Azure AD. Составьте список имен отображений групп для синхронизации с Azure AD DS.

1. Выполнить [сценарий из предыдущего раздела](#powershell-script-for-scoped-synchronization) и использовать *параметр -groupsToAdd* для передачи списка групп для синхронизации.

   > [!WARNING]
   > Необходимо включить группу *администраторов AAD DC* в список групп для синхронизации. Если вы не включаете эту группу, управляемый домен Azure AD DS непригоен для угобы.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Теперь создайте управляемый домен Azure AD DS и создайте синхронизацию с областью групповой синхронизации. Включите *параметр "filteredSync" и "Enabled".* *-Properties*

    Установите идентификатор подписки Azure, а затем укажите имя для управляемого домена, например *aaddscontoso.com.* Вы можете получить идентификатор подписки с помощью cmdlet [Get-AzSubscription.][Get-AzSubscription] Установите имя группы ресурсов, имя виртуальной сети и область к значениям, используемым в предыдущих шагах, для создания вспомогательных ресурсов Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
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

Создание ресурса и возврат управления в запрос PowerShell занимает несколько минут. Управляемый домен Azure AD DS продолжает быть подготовлен в фоновом режиме и может занять до часа для завершения развертывания. На портале Azure страница **«Обзор»** для домена Azure AD DS показывает текущее состояние на протяжении всего этапа развертывания.

Когда портал Azure показывает, что управляемый домен Azure AD DS закончил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите на портале управляемый DS AD Azure AD. В окне **Обзора** вам предлагается автоматически настроить настройки DNS.
* Если в регионе, поддерживающем зоны доступности, создан домен Azure AD DS, создайте группу сетевой безопасности для ограничения трафика в виртуальной сети для домена Azure AD DS. Создается стандартный балансер нагрузки Azure, который требует размещения этих правил. Эта группа сетевой безопасности обеспечивает безопасность DS Azure AD и необходима для правильной работы управляемого домена.
    * Чтобы создать группу сетевой безопасности и требуемые правила, выберите на портале управляемый DS AD Azure. В окне **Обзора** предлагается автоматически создавать и настраивать группу сетевой безопасности.
* [Позволяет синхронизировать пароли в службы домена Azure AD,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) чтобы конечные пользователи могли войти в управляемый домен, используя свои корпоративные учетные данные.

## <a name="modify-scoped-synchronization-using-powershell"></a>Изменение масштабной синхронизации с помощью PowerShell

Чтобы изменить список групп, пользователи которых должны быть синхронизированы с доменом Azure AD DS, перезапустите [скрипт PowerShell](#powershell-script-for-scoped-synchronization) и укажите новый список групп. В следующем примере группы для синхронизации больше не включают *GroupName2,* а теперь включают *GroupName3.*

> [!WARNING]
> Необходимо включить группу *администраторов AAD DC* в список групп для синхронизации. Если вы не включаете эту группу, управляемый домен Azure AD DS непригоен для угобы.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Изменение сферы синхронизации приводит к тому, что управляемый домен Azure AD DS пересинхронизирует все данные. Объекты, которые больше не требуются в домене Azure AD DS, удаляются, и ресинхронизация может занять много времени.

## <a name="disable-scoped-synchronization-using-powershell"></a>Отключение синхронизации с использованием PowerShell

Чтобы отключить синхронизацию групповых областей для управляемого домена Azure AD DS, установите *"filteredSync" и "Disabled"* на ресурсе Azure AD DS, а затем обновите управляемый домен. После завершения все пользователи и группы настроены на синхронизацию с Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Изменение сферы синхронизации приводит к тому, что управляемый домен Azure AD DS пересинхронизирует все данные. Объекты, которые больше не требуются в домене Azure AD DS, удаляются, и ресинхронизация может занять много времени.

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о процессе синхронизации читайте [в синхронизации Azure AD Domain Services.](synchronization.md)

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
