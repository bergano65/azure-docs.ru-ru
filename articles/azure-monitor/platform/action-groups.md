---
title: Создание групп действий и управление ими на портале Azure
description: Узнайте, как создавать группы действий и управлять ими на портале Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705258"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Создание групп действий и управление ими на портале Azure
Группа действий — это коллекция параметров уведомлений, которые определены владельцем подписки Azure. Оповещения служб Azure Monitor и "Работоспособность служб" используют группы действий для уведомления пользователей о том, что оповещение активировано. Для разных оповещений может использоваться как одна группа действий, так разные. Это зависит от требований пользователя. В подписке можно настроить до 2000 групп действий.

Можно настроить действие, для уведомления пользователя по электронной почте или SMS, они получают подтверждающее сообщение, указывающее, что они были добавлены в группу действий.

В этой статье показано, как создавать группы действий и управлять ими на портале Azure.

Каждое действие состоит из следующих свойств:

* **Имя**: уникальный идентификатор в группе действий.  
* **Тип действия:** Выполнить действие. Примеры включают отправку голосового звонка, текстовое сообщение, сообщение электронной почты или активацию различных типов автоматических действий. Типы приведены далее в этой статье.
* **Подробности:** Соответствующие сведения, которые зависят от *тип действия*.

Дополнительные сведения о настройке групп действий с помощью шаблонов Azure Resource Manager см. в статье [Создание группы действий с помощью шаблона Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Создание группы действий с помощью портала Azure

1. На [портале Azure](https://portal.azure.com) выберите **Монитор**. **Монитор** области объединяет всех параметров мониторинга и данных в одном представлении.

    ![Служба "Монитор"](./media/action-groups/home-monitor.png)
    
1. Выберите **оповещения** выберите **Управление действиями**.

    ![Управление кнопкой "действия"](./media/action-groups/manage-action-groups.png)
    
1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

    ![Команда Add action group (Добавить группу действий)](./media/action-groups/add-action-group.png)
    
1. Введите имя в поля **Action group name** (Имя группы действий) и **Short name** (Короткое имя). Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

      ![Диалоговое окно Add action group (Добавить группу действий)](./media/action-groups/action-group-define.png)

1. Поле **Подписка** автоматически заполняется вашей текущей подпиской. В этой подписке сохраняются группы действий.

1. Выберите **группу ресурсов**, в которой хранится группа действий.

1. Определите список действий. Предоставьте следующие данные для каждого действия:

    1. **Имя.** введите уникальный идентификатор для этого действия.

    1. **Тип действия.** выберите Email/SMS/Push/Voice (Электронная почта, SMS, push-уведомление, голосовой вызов), приложение логики, веб-перехватчик, ITSM или модуль runbook службы автоматизации.

    1. **Подробности:** в зависимости от выбранного типа действия укажите номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика, приложение Azure, сведения о подключении ITSM или модуль runbook службы автоматизации. Для действия ITSM дополнительно укажите **рабочий элемент** и заполните другие поля, требуемые инструментом ITSM.
    
    1. **Общая схема предупреждения**: Вы можете включить [общей схеме оповещений](https://aka.ms/commonAlertSchemaDocs), который предоставляет преимущества размещения одной расширяемой и служб единой полезных данных оповещения всех оповещений в Azure Monitor.

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

## <a name="manage-your-action-groups"></a>Управление группами действий

После создания группы действий, он отображается в **группы действий** раздел **монитор** области. Выберите группу действий, которой необходимо управлять:

* добавлять, изменять или удалять действия;
* удалить группу действий.

## <a name="action-specific-information"></a>Сведения о конкретных действиях

> [!NOTE]
> См. в разделе [ограничения службы подписки для мониторинга](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) числовые ограничения на каждом из следующих ссылок.  

### <a name="azure-app-push-notifications"></a>Push-уведомлений приложений Azure
Имеется ограниченное число действий приложений Azure в группе действий.

### <a name="email"></a>Email
Электронная почта будет отправляться на следующие адреса (правильно настройте фильтрацию электронной почты):
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Имеется ограниченное число действий электронной почты в группе действий. См. в разделе [тарифа сведения](./../../azure-monitor/platform/alerts-rate-limiting.md) статьи.

### <a name="itsm"></a>ITSM
Действие ITSM требует подключения ITSM. Дополнительные сведения о создании подключения ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).

Имеется ограниченное число действий ITSM в группе действий. 

### <a name="logic-app"></a>приложение логики;
Имеется ограниченное число действий приложения логики в группе действий.

### <a name="function"></a>Функция
Функциональные клавиши для приложений-функций настроен в качестве действия доступны для чтения через API функции, который в данный момент требует, чтобы настроить параметр «AzureWebJobsSecretStorageType» в «файлы» приложения приложение функции v2. Дополнительные сведения см. в разделе [изменения управления ключами в версии 2 функции]( https://aka.ms/funcsecrets).

Имеется ограниченный набор функций действий в группе действий.

### <a name="automation-runbook"></a>Runbook службы автоматизации
Ссылаться на [ограничения службы подписки Azure](../../azure-subscription-service-limits.md) об ограничениях в полезные данные Runbook.

Имеется ограниченный набор действий Runbook в группе действий. 

### <a name="sms"></a>SMS
См. в разделе [тарифа сведения](./../../azure-monitor/platform/alerts-rate-limiting.md) и [поведение SMS-оповещений](../../azure-monitor/platform/alerts-sms-behavior.md) дополнительные важные сведения.

Имеется ограниченное число действий SMS в группе действий.  

### <a name="voice"></a>Голосовая связь
См. в разделе [тарифа сведения](./../../azure-monitor/platform/alerts-rate-limiting.md) статьи.

Имеется ограниченное число действий голосовой связи в группе действий.

### <a name="webhook"></a>webhook
Веб-перехватчики повторяются, используя следующие правила. Вызов веб-перехватчика повторяется более 2 раза при следующих кодов состояния HTTP возвращаются: 408, 429, 503, 504 (или если конечная точка HTTP не отвечает). Первый повторный вызов происходит через 10 секунд, второй — через 100 секунд. После двух отказов ни одной группы действий вызовет конечную точку для 30 минут. 

Исходные диапазоны IP-адресов
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Для получения обновлений о внесении изменений в эти IP-адреса, мы рекомендуем настроить оповещение о работоспособности службы, которая отслеживает для уведомления о службе группы действий.

Имеется ограниченное число действий веб-перехватчика в группе действий.

#### <a name="secure-webhook"></a>Защита веб-перехватчика
**Функция защиты веб-перехватчика в настоящий момент в предварительной версии.**

Действие Webhook группы действий можно воспользоваться преимуществами Azure Active Directory для обеспечения безопасного подключения между вашей группы действий и защищенных веб-API (конечная точка веб-перехватчика). Ниже описан общий рабочий процесс для использования этой функции. Обзор приложения Azure AD и субъекты-службы, см. в разделе [обзор платформы (версии 2.0) Microsoft identity](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Создайте приложение Azure AD для защищенного веб-API. См. раздел https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Настройка вашей защищенный API, которая будет вызываться управляющую программу.
    
1. Включите группы действий для использования приложения Azure AD.

    > [!NOTE]
    > Необходимо быть членом [роли администратора приложения Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) для выполнения этого сценария.
    
    - Измените вызов Connect-AzureAD сценарий PowerShell для использования идентификатора клиента Azure AD.
    - Изменить сценарий PowerShell переменную $myAzureADApplicationObjectId следует использовать идентификатор объекта приложения Azure AD
    - Запустите измененный скрипт.
    
1. Настройка действия веб-перехватчик для действий группы.
    - Скопируйте значение $myApp.ObjectId из скрипта и введите его в поле "идентификатор объекта приложения" в определении действия веб-перехватчика.
    
    ![Действие веб-перехватчика безопасности](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Защита сценарий PowerShell веб-перехватчика

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


## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения о поведении SMS-оповещений в группе действий см. в [этой статье](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Узнайте о [схеме веб-перехватчика для оповещений журнала действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Дополнительные сведения о соединителе ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).
* Дополнительные сведения о лимитах для оповещений см. в статье [Ограничение частоты отправки для SMS, сообщений электронной почты и вызовов Webhook](../../azure-monitor/platform/alerts-rate-limiting.md).
* Изучите [обзор оповещений журнала действий](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
