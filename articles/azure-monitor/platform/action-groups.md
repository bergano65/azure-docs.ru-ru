---
title: Создание групп действий и управление ими на портале Azure
description: Узнайте, как создавать группы действий и управлять ими на портале Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 9bc191bb27ebb0bac631ef5cfa8ddc34bbd8214e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520893"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Создание групп действий и управление ими на портале Azure
Группа действий — это коллекция параметров уведомлений, которые определены владельцем подписки Azure. Оповещения служб Azure Monitor и "Работоспособность служб" используют группы действий для уведомления пользователей о том, что оповещение активировано. Для разных оповещений может использоваться как одна группа действий, так разные. Это зависит от требований пользователя. В подписке можно настроить до 2000 групп действий.

Вы настраиваете действие, чтобы уведомить человека по электронной почте или SMS, они получают подтверждение о том, что они были добавлены в группу действий.

В этой статье показано, как создавать группы действий и управлять ими на портале Azure.

Каждое действие состоит из следующих свойств:

* **Имя**: Уникальный идентификатор в группе действий.  
* **Тип действия**: Выполнено действие. Примеры включают отправку голосового звонка, текстовое сообщение, сообщение электронной почты или активацию различных типов автоматических действий. Типы приведены далее в этой статье.
* **Детали**: Соответствующие детали, которые варьируются в зависимости от *типа действия.*

Дополнительные сведения о настройке групп действий с помощью шаблонов Azure Resource Manager см. в статье [Создание группы действий с помощью шаблона Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Создание группы действий с помощью портала Azure

1. На [портале Azure](https://portal.azure.com)ищите и выберите **Monitor.** Панель **монитора** объединяет все настройки мониторинга и данные в одном представлении.

1. Выберите **Оповещения**, а затем **Управление действиями**.

    ![Кнопка управления действиями](./media/action-groups/manage-action-groups.png)
    
1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

    ![Команда Add action group (Добавить группу действий)](./media/action-groups/add-action-group.png)
    
1. Введите имя в поле **имени группы Action** и введите имя в поле **Короткого имени.** Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

      ![Диалоговое окно Add action group (Добавить группу действий)](./media/action-groups/action-group-define.png)

1. Коробка **подписки** автоматически заполняется текущей подпиской. В этой подписке сохраняются группы действий.

1. Выберите **группу ресурсов,** в которой сохраняется группа действий.

1. Определите список действий. Предоставьте следующее для каждого действия:

    1. **Имя.** Введите уникальный идентификатор для этого действия.

    1. **Тип действий**: Выберите автоматизацию Runbook, функция Azure, роль менеджера ресурсов Azure, email/SMS/Push/Voice, ITSM, Logic App, Безопасный Webhook, Webhook.

    1. **Подробные сведения**. В зависимости от выбранного типа действия укажите номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика, приложение Azure, сведения о подключении ITSM или модуль runbook службы автоматизации. Для действия ITSM дополнительно укажите **рабочий элемент** и заполните другие поля, требуемые инструментом ITSM.
    
    1. **Распространенная схема оповещения**: Вы можете включить [общую схему оповещения,](https://aka.ms/commonAlertSchemaDocs)которая обеспечивает преимущество наличия единой расширяемой и единой полезной нагрузки оповещения во всех службах оповещения в Azure Monitor.

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

## <a name="manage-your-action-groups"></a>Управление группами действий

После создания группы действий можно просмотреть **группы действий,** выбрав **действия Управления** с целевой **страницы оповещения** в панели **монитора.** Выберите группу действий, которой необходимо управлять:

* добавлять, изменять или удалять действия;
* удалить группу действий.

## <a name="action-specific-information"></a>Сведения о конкретных действиях

> [!NOTE]
> Просмотрите [ограничения службподписки для мониторинга](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) для количественных ограничений по каждому из элементов, приведенных ниже.  

### <a name="automation-runbook"></a>Автоматизация Runbook
Обратитесь к [ограничениям службы подписки Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) для ограничений на полезные нагрузки Runbook.

В группе действий может быть ограничено количество действий Runbook. 

### <a name="azure-app-push-notifications"></a>Уведомление приложения Azure Push
В группе действий может быть ограниченное количество действий приложения Azure.

### <a name="email"></a>Email
Электронная почта будет отправляться на следующие адреса (правильно настройте фильтрацию электронной почты):
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

В Группе действий может быть ограничено количество действий по электронной почте. Смотрите статью о [ограничении скорости информации.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Роль менеджера ресурсов Azure по электронной почте
Отправить электронное письмо участникам роли подписки. Электронная почта будет отправлена только **пользователям AD-сообщения Azure AD.** Электронная почта не будет отправлена группам Azure AD или директорам служб.

В Группе действий может быть ограничено количество действий по электронной почте. Смотрите статью о [ограничении скорости информации.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Функция
Вызывает существующую конечную точку триггера HTTP в [функциях Azure.](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)

В Группе действий может быть ограничено количество действий.

### <a name="itsm"></a>ITSM
Действие ITSM требует подключения ITSM. Дополнительные сведения о создании подключения ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).

В группе действий может быть ограничено количество действий ITSM. 

### <a name="logic-app"></a>приложение логики;
В группе действий может быть ограничено количество действий Logic App.

### <a name="secure-webhook"></a>Безопасный Вебхук
Действие Action Groups Webhook позволяет использовать active Directory Azure Для обеспечения связи между группой действий и защищенным web API (конечная точка веб-хука). Общий рабочий процесс для использования этой функциональности описан ниже. Для обзора приложений azure AD и основ [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)службы см.

1. Создайте приложение Azure AD для защищенного веб-API. См. раздел https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Наверстримируйте защищенный API, который будет вызываться приложением daemon.
    
1. Позволяет группам действий использовать приложение Azure AD.

    > [!NOTE]
    > Для выполнения этого сценария необходимо стать участником [роли администратора приложений Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)
    
    - Измените вызов скрипта Connect-AzureAD для использования идентификатора aD-идентификатора Azure AD.
    - Изменить переменную $myAzureADApplicationObjectId скрипта PowerShell, чтобы использовать идентификатор объекта приложения Azure AD
    - Выполнить измененный скрипт.
    
1. Настройте действие группы действий Безопасный Webhook действий.
    - Копируйте значение $myApp.ObjectId из скрипта и введите его в поле идентификатора объектов приложения в определении действия Webhook.
    
    ![Безопасные действия Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Безопасный сценарий Webhook PowerShell

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
Ознакомьтесь с [скоростью, ограничивающей информацию,](./../../azure-monitor/platform/alerts-rate-limiting.md) и [поведением SMS-оповещения](../../azure-monitor/platform/alerts-sms-behavior.md) для получения дополнительной важной информации.

В Группе действий может быть ограничено количество SMS-действий.  

### <a name="voice"></a>Голосовая связь
Смотрите статью о [ограничении скорости информации.](./../../azure-monitor/platform/alerts-rate-limiting.md)

В группе действий может быть ограничено количество действий «Голоса».

### <a name="webhook"></a>webhook
Веб-крючки повторяются с использованием следующих правил. Вызов webhook выполняется не более 2 раз, когда возвращаются следующие коды статуса HTTP: 408, 429, 503, 504 или конечная точка HTTP. Первый повторный вызов происходит через 10 секунд, второй — через 100 секунд. После двух сбоев ни одна группа действий не вызовет конечную точку в течение 30 минут. 

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

Для получения обновлений об изменениях на этих IP-адресах мы рекомендуем настроить оповещение о работоспособности службы, которое отслеживает информационные уведомления о службе групп действий.

В Группе действий может быть ограничено количество действий Webhook.



## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о поведении SMS-оповещений в группе действий см. в [этой статье](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Узнайте о [схеме веб-перехватчика для оповещений журнала действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Дополнительные сведения о соединителе ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).
* Подробнее об [ограничении скорости](../../azure-monitor/platform/alerts-rate-limiting.md) в оповещениях.
* Изучите [обзор оповещений журнала действий](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
