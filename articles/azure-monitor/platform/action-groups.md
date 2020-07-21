---
title: Создание групп действий и управление ими на портале Azure
description: Узнайте, как создавать группы действий и управлять ими на портале Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: e88d51e014244892fc3ac9e2cca242dacdfd9997
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516181"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Создание групп действий и управление ими на портале Azure
Группа действий — это коллекция параметров уведомлений, которые определены владельцем подписки Azure. Оповещения служб Azure Monitor и "Работоспособность служб" используют группы действий для уведомления пользователей о том, что оповещение активировано. Для разных оповещений может использоваться как одна группа действий, так разные. Это зависит от требований пользователя. В подписке можно настроить до 2000 групп действий.

Можно настроить действие для уведомления пользователя по электронной почте или SMS. Тогда этот пользователь получит подтверждение о добавлении его в группу действий.

В этой статье показано, как создавать группы действий и управлять ими на портале Azure.

Каждое действие состоит из следующих свойств:

* **Name** (Имя). уникальный идентификатор в группе действий.  
* **Тип действия:** выполненные операции; Примеры включают отправку голосового звонка, текстовое сообщение, сообщение электронной почты или активацию различных типов автоматических действий. Типы приведены далее в этой статье.
* **Подробности:** соответствующие сведения, которые зависят от *типа действия*.

Дополнительные сведения о настройке групп действий с помощью шаблонов Azure Resource Manager см. в статье [Создание группы действий с помощью шаблона Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Создание группы действий с помощью портала Azure

1. На [портале Azure](https://portal.azure.com) найдите и выберите область **Монитор**. В области **Монитор** объединены все параметры мониторинга и данные.

1. Выберите **Оповещения**, а затем **Управление действиями**.

    ![Кнопка "Управление действиями"](./media/action-groups/manage-action-groups.png)
    
1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

    ![Команда Add action group (Добавить группу действий)](./media/action-groups/add-action-group.png)
    
1. Введите имя в поля **Action group name** (Имя группы действий) и **Short name** (Короткое имя). Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

      ![Диалоговое окно Add action group (Добавить группу действий)](./media/action-groups/action-group-define.png)

1. Поле **Подписка** автоматически заполняется вашей текущей подпиской. В этой подписке сохраняются группы действий.

1. Выберите **группу ресурсов**, в которой хранится группа действий.

1. Определите список действий. Для каждого действия укажите следующие сведения.

    1. **Name** (Имя). введите уникальный идентификатор для этого действия.

    1. **Тип действия.** Выберите runbook службы автоматизации, Функцию Azure, роль рассылки электронной почты Azure Resource Manager, электронную почту, SMS, push-уведомление или голосовое сообщение, ITSM, приложение логики, безопасный веб-перехватчик либо веб-перехватчик.

    1. **Подробности:** в зависимости от выбранного типа действия укажите номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика, приложение Azure, сведения о подключении ITSM или модуль runbook службы автоматизации. Для действия ITSM дополнительно укажите **рабочий элемент** и заполните другие поля, требуемые инструментом ITSM.
    
    1. **Общая схема оповещений** Можно включить [общую схему оповещений](https://aka.ms/commonAlertSchemaDocs), которая дает преимущества единых расширяемых и унифицированных полезных данных оповещений во всех службах оповещений в Azure Monitor.

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

## <a name="manage-your-action-groups"></a>Управление группами действий

После создания группы действий можно просмотреть **группы действий**, выбрав **Управление действиями** на целевой странице **Оповещения** в области **Монитор**. Выберите группу действий, которой необходимо управлять:

* добавлять, изменять или удалять действия;
* удалить группу действий.

## <a name="action-specific-information"></a>Сведения о конкретных действиях

> [!NOTE]
> Количественные ограничения для каждого из перечисленных ниже пунктов приведены в разделе [Ограничения Azure Monitor](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).  

### <a name="automation-runbook"></a>Runbook автоматизации
Дополнительные сведения об ограничениях для полезных данных runbook см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-resource-manager/management/azure-subscription-service-limits.md).

В группе действий может быть ограниченное число действий runbook. 

### <a name="azure-app-push-notifications"></a>Push-уведомления приложения Azure
В группе действий может быть ограниченное число действий приложения Azure.

### <a name="email"></a>Email
Электронная почта будет отправляться на следующие адреса (правильно настройте фильтрацию электронной почты):
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

В группе действий может быть ограниченное число действий электронной почты. Дополнительные сведения см. в статье [Ограничение частоты отправки для голосовых сообщений, SMS-сообщений, сообщений электронной почты, push-уведомлений приложений Azure и записей веб-перехватчиков](./../../azure-monitor/platform/alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Роль Azure Resource Manager по электронной почте
Электронное сообщение отправляется членам роли подписки. Электронное сообщение будет отправлено только членам роли **пользователя Azure AD**. Сообщение электронной почты будет отправлено группам или субъектам-служб Azure AD.

В группе действий может быть ограниченное число действий электронной почты. Дополнительные сведения см. в статье [Ограничение частоты отправки для голосовых сообщений, SMS-сообщений, сообщений электронной почты, push-уведомлений приложений Azure и записей веб-перехватчиков](./../../azure-monitor/platform/alerts-rate-limiting.md).

### <a name="function"></a>Компонент
Вызывает существующую конечную точку триггера HTTP в [Функциях Azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

В группе действий может быть ограниченное число действий Функций.

### <a name="itsm"></a>ITSM
Действие ITSM требует подключения ITSM. Дополнительные сведения о создании подключения ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).

В группе действий может быть ограниченное число действий ITSM. 

### <a name="logic-app"></a>приложение логики;
В группе действий может быть ограниченное число действий приложения логики.

### <a name="secure-webhook"></a>Безопасный веб-перехватчик
Действие веб-перехватчика группы действий позволяет воспользоваться преимуществами Azure Active Directory для защиты подключения между группой действий и защищенным веб-API (конечной точкой веб-перехватчика). Ниже описана общая процедура использования этой функции. Общие сведения о приложениях и субъектах-службах Azure AD см. в статье [Общие сведения о платформе удостоверений Майкрософт версии 2.0](../../active-directory/develop/v2-overview.md).

1. Создайте приложение Azure AD для защищенного веб-API. См. раздел [защищенный веб-API: регистрация приложения](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration).
    - Настройте защищенный API, который будет [вызываться приложением демона](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#if-your-web-api-is-called-by-a-daemon-app).
    
2. Включите группы действий для использования приложения Azure AD.

    > [!NOTE]
    > Для выполнения этого сценария необходимо быть членом [роли администратора приложения Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles).
    
    - Измените вызов Connect-AzureAD в сценарии PowerShell, чтобы использовать свой идентификатор арендатора Azure AD.
    - Измените переменную скрипта PowerShell $myAzureADApplicationObjectId, чтобы использовать идентификатор объекта приложения Azure AD.
    - Запустите измененный сценарий.
    
3. Настройте действие безопасного веб-перехватчика группы действий.
    - Скопируйте значение $myApp.ObjectId из сценария и введите его в поле идентификатора объекта приложения в определении действия веб-перехватчика.
    
    ![Безопасное действие веб-перехватчика](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Сценарий PowerShell безопасного веб-перехватчика

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
Дополнительные важные сведения см. в разделах [Ограничение частоты отправки для голосовых сообщений, SMS-сообщений, сообщений электронной почты, push-уведомлений приложений Azure и записей веб-перехватчиков](./../../azure-monitor/platform/alerts-rate-limiting.md) и [Поведение SMS-оповещений в группе действий](../../azure-monitor/platform/alerts-sms-behavior.md). 

В группе действий может быть ограниченное число действий SMS.

> [!NOTE]
> Если пользовательский интерфейс группы действий на портале Azure не позволяет выбрать ваш код страны или региона, то функция отправки SMS для вашей страны или региона не поддерживается.  Если код страны или региона недоступен, можно проголосовать за добавление вашей страны или региона на портале [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). А пока в качестве обходного пути можно настроить группу действий так, чтобы она вызывала веб-перехватчик для стороннего поставщика SMS, который поддерживает вашу страны или регион.  

Цены для поддерживаемых страны и регионов перечислены на [странице цен на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Голосовая связь
Дополнительные важные сведения о поведении см. в статье [Ограничение частоты отправки для голосовых сообщений, SMS-сообщений, сообщений электронной почты, push-уведомлений приложений Azure и записей веб-перехватчиков](./../../azure-monitor/platform/alerts-rate-limiting.md).

В группе действий может быть ограниченное число голосовых действий.

> [!NOTE]
> Если пользовательский интерфейс группы действий на портале Azure не позволяет выбрать ваш код страны или региона, то голосовые звонки для вашей страны или региона не поддерживаются. Если код страны или региона недоступен, можно проголосовать за добавление вашей страны или региона на портале [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  А пока в качестве обходного пути можно настроить группу действий так, чтобы она вызывала веб-перехватчик для стороннего поставщика голосовой связи, который поддерживает вашу страны или регион.  

Цены для поддерживаемых страны и регионов перечислены на [странице цен на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook
Веб-перехватчики обрабатываются с использованием следующих правил
- Число попыток вызова веб-перехватчика не более 3 раз.
- Вызов будет повторен, если ответ не получен в течение периода ожидания или возвращен один из следующих кодов состояния HTTP: 408, 429, 503 или 504.
- Первый вызов будет ожидать ответа в течение 10 секунд.
- Вторая и третья попытки будут ожидать ответа в течение 30 секунд.
- После 3 попыток вызова веб-перехватчика группа действий не будет вызывать конечную точку в течение 15 минут.

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

Для получения обновлений об изменениях этих IP-адресов рекомендуется настроить оповещение о работоспособности служб, которое отслеживает информационные уведомления о службе группы действий.

В группе действий может быть ограниченное число действий веб-перехватчика.



## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о поведении SMS-оповещений в группе действий см. в [этой статье](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Узнайте о [схеме веб-перехватчика для оповещений журнала действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Дополнительные сведения о [соединитель ITSM](../../azure-monitor/platform/itsmc-overview.md).
* Дополнительные сведения о лимитах для оповещений см. в статье [Ограничение частоты отправки для SMS, сообщений электронной почты и вызовов Webhook](../../azure-monitor/platform/alerts-rate-limiting.md).
* Изучите [обзор оповещений журнала действий](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](../../service-health/alerts-activity-log-service-notifications-portal.md).
