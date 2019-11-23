---
title: Создание групп действий и управление ими на портале Azure
description: Узнайте, как создавать группы действий и управлять ими на портале Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6b3d1ff76d4f7611da8e08dd4ce42293c805978e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423852"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Создание групп действий и управление ими на портале Azure
Группа действий — это коллекция параметров уведомлений, которые определены владельцем подписки Azure. Оповещения служб Azure Monitor и "Работоспособность служб" используют группы действий для уведомления пользователей о том, что оповещение активировано. Для разных оповещений может использоваться как одна группа действий, так разные. Это зависит от требований пользователя. В подписке можно настроить до 2000 групп действий.

You configure an action to notify a person by email or SMS, they receive a confirmation indicating they have been added to the action group.

В этой статье показано, как создавать группы действий и управлять ими на портале Azure.

Каждое действие состоит из следующих свойств:

* **Имя:** уникальный идентификатор в группе действий.  
* **Action type**: The action performed. Примеры включают отправку голосового звонка, текстовое сообщение, сообщение электронной почты или активацию различных типов автоматических действий. Типы приведены далее в этой статье.
* **Details**: The corresponding details that vary by *action type*.

Дополнительные сведения о настройке групп действий с помощью шаблонов Azure Resource Manager см. в статье [Создание группы действий с помощью шаблона Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Создание группы действий с помощью портала Azure

1. In the [Azure portal](https://portal.azure.com), search for and select **Monitor**. The **Monitor** pane consolidates all your monitoring settings and data in one view.

1. Выберите **Оповещения**, а затем **Управление действиями**.

    ![Manage Actions button](./media/action-groups/manage-action-groups.png)
    
1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

    ![Команда Add action group (Добавить группу действий)](./media/action-groups/add-action-group.png)
    
1. Введите имя в поля **Action group name** (Имя группы действий) и **Short name** (Короткое имя). Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

      ![Диалоговое окно Add action group (Добавить группу действий)](./media/action-groups/action-group-define.png)

1. Поле **Подписка** автоматически заполняется вашей текущей подпиской. В этой подписке сохраняются группы действий.

1. Выберите **группу ресурсов**, в которой хранится группа действий.

1. Define a list of actions. Provide the following for each action:

    1. **Имя.** Введите уникальный идентификатор для этого действия.

    1. **Тип действия**. Вы можете выбрать Email/SMS/Push/Voice (Электронная почта, SMS, push-уведомление, голосовой вызов), приложение логики, веб-перехватчик, ITSM или модуль runbook службы автоматизации.

    1. **Подробные сведения**. В зависимости от выбранного типа действия укажите номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика, приложение Azure, сведения о подключении ITSM или модуль runbook службы автоматизации. Для действия ITSM дополнительно укажите **рабочий элемент** и заполните другие поля, требуемые инструментом ITSM.
    
    1. **Common alert schema**: You can choose to enable the [common alert schema](https://aka.ms/commonAlertSchemaDocs), which provides the advantage of having a single extensible and unified alert payload across all the alert services in Azure Monitor.

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

## <a name="manage-your-action-groups"></a>Управление группами действий

After you create an action group, it's visible in the **Action groups** section of the **Monitor** pane. Выберите группу действий, которой необходимо управлять:

* добавлять, изменять или удалять действия;
* удалить группу действий.

## <a name="action-specific-information"></a>Сведения о конкретных действиях

> [!NOTE]
> See [Subscription Service Limits for Monitoring](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) for numeric limits on each of the items below.  

### <a name="automation-runbook"></a>Automation Runbook
Refer to the [Azure subscription service limits](../../azure-subscription-service-limits.md) for limits on Runbook payloads.

You may have a limited number of Runbook actions in an Action Group. 

### <a name="azure-app-push-notifications"></a>Azure app Push Notifications
You may have a limited number of Azure app actions in an Action Group.

### <a name="email"></a>Эл. почта
Электронная почта будет отправляться на следующие адреса (правильно настройте фильтрацию электронной почты):
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="email-azure-resource-manager-role"></a>Email Azure Resource Manager Role
Send email to the members of the subscription's role.

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="function"></a>Функция
The function keys for Function Apps configured as actions are read through the Functions API, which currently requires v2 function apps to configure the app setting “AzureWebJobsSecretStorageType” to “files”. For more information, see [Changes to Key Management in Functions V2]( https://aka.ms/funcsecrets).

You may have a limited number of Function actions in an Action Group.

### <a name="itsm"></a>ITSM
Действие ITSM требует подключения ITSM. Дополнительные сведения о создании подключения ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).

You may have a limited number of ITSM actions in an Action Group. 

### <a name="logic-app"></a>Логическое приложение
You may have a limited number of Logic App actions in an Action Group.

### <a name="secure-webhook"></a>Secure Webhook
**The Secure Webhook functionality is currently in Preview.**

The Action Groups Webhook action enables you to take advantage of Azure Active Directory to secure the connection between your action group and your protected web API (webhook endpoint). The overall workflow for taking advantage of this functionality is described below. For an overview of Azure AD Applications and service principals, see [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Create an Azure AD Application for your protected web API. См. https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure your protected API to be called by a daemon app.
    
1. Enable Action Groups to use your Azure AD Application.

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
    - Modify the PowerShell script's Connect-AzureAD call to use your Azure AD Tenant ID.
    - Modify the PowerShell script's variable $myAzureADApplicationObjectId to use the Object ID of your Azure AD Application
    - Run the modified script.
    
1. Configure the Action Group Secure Webhook action.
    - Copy the value $myApp.ObjectId from the script and enter it in the Application Object ID field in the Webhook action definition.
    
    ![Secure Webhook action](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure Webhook PowerShell Script

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) and [SMS alert behavior](../../azure-monitor/platform/alerts-sms-behavior.md) for additional important information.

You may have a limited number of SMS actions in an Action Group.  

### <a name="voice"></a>Голос
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

You may have a limited number of Voice actions in an Action Group.

### <a name="webhook"></a>webhook
Webhooks are retried using the following rules. The webhook call is retried a maximum of 2 times when the following HTTP status codes are returned: 408, 429, 503, 504 or the HTTP endpoint does not respond. Первый повторный вызов происходит через 10 секунд, второй — через 100 секунд. After two failures, no action group will call the endpoint for 30 minutes. 

Исходные диапазоны IP-адресов
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

To receive updates about changes to these IP addresses, we recommend you configure a Service Health alert, which monitors for Informational notifications about the Action Groups service.

You may have a limited number of Webhook actions in an Action Group.



## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о поведении SMS-оповещений в группе действий см. в [этой статье](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Узнайте о [схеме веб-перехватчика для оповещений журнала действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Дополнительные сведения о соединителе ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).
* Дополнительные сведения о лимитах для оповещений см. в статье [Ограничение частоты отправки для SMS, сообщений электронной почты и вызовов Webhook](../../azure-monitor/platform/alerts-rate-limiting.md).
* Изучите [обзор оповещений журнала действий](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
