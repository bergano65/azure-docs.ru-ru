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
ms.openlocfilehash: a0b0df9110f062b5f9c23840cb21308b634c9c81
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898154"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Создание групп действий и управление ими на портале Azure
Группа действий — это коллекция параметров уведомлений, которые определены владельцем подписки Azure. Оповещения служб Azure Monitor и "Работоспособность служб" используют группы действий для уведомления пользователей о том, что оповещение активировано. Для разных оповещений может использоваться как одна группа действий, так разные. Это зависит от требований пользователя. В подписке можно настроить до 2000 групп действий.

Вы настраиваете действие для уведомления пользователя по электронной почте или SMS, оно получит подтверждение о том, что они были добавлены в группу действий.

В этой статье показано, как создавать группы действий и управлять ими на портале Azure.

Каждое действие состоит из следующих свойств:

* **Имя**: уникальный идентификатор в группе действий.  
* **Тип действия:** Выполненное действие. Примеры включают отправку голосового звонка, текстовое сообщение, сообщение электронной почты или активацию различных типов автоматических действий. Типы приведены далее в этой статье.
* **Сведения**. Соответствующие сведения, зависящие от *типа действия*.

Дополнительные сведения о настройке групп действий с помощью шаблонов Azure Resource Manager см. в статье [Создание группы действий с помощью шаблона Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Создание группы действий с помощью портала Azure

1. На [портале Azure](https://portal.azure.com) выберите **Монитор**. Панель **монитор** объединяет все параметры мониторинга и данные в одном представлении.

    ![Служба "Монитор"](./media/action-groups/home-monitor.png)
    
1. Выберите **оповещения** и щелкните **Управление действиями**.

    ![Кнопка "Управление действиями"](./media/action-groups/manage-action-groups.png)
    
1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

    ![Команда Add action group (Добавить группу действий)](./media/action-groups/add-action-group.png)
    
1. Введите имя в поля **Action group name** (Имя группы действий) и **Short name** (Короткое имя). Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

      ![Диалоговое окно Add action group (Добавить группу действий)](./media/action-groups/action-group-define.png)

1. Поле **Подписка** автоматически заполняется вашей текущей подпиской. В этой подписке сохраняются группы действий.

1. Выберите **группу ресурсов**, в которой хранится группа действий.

1. Определите список действий. Для каждого действия укажите следующие сведения.

    1. **Имя.** введите уникальный идентификатор для этого действия.

    1. **Тип действия.** выберите Email/SMS/Push/Voice (Электронная почта, SMS, push-уведомление, голосовой вызов), приложение логики, веб-перехватчик, ITSM или модуль runbook службы автоматизации.

    1. **Сведения**. в зависимости от выбранного типа действия укажите номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика, приложение Azure, сведения о подключении ITSM или модуль runbook службы автоматизации. Для действия ITSM дополнительно укажите **рабочий элемент** и заполните другие поля, требуемые инструментом ITSM.
    
    1. **Общая схема предупреждений**: Вы можете включить [общую схему предупреждений](https://aka.ms/commonAlertSchemaDocs), которая предоставляет преимущества единого расширяемого и унифицированного набора полезных данных оповещений во всех службах предупреждений в Azure Monitor.

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

## <a name="manage-your-action-groups"></a>Управление группами действий

После создания группы действий она отображается в разделе **группы действий** панели **монитор** . Выберите группу действий, которой необходимо управлять:

* добавлять, изменять или удалять действия;
* удалить группу действий.

## <a name="action-specific-information"></a>Сведения о конкретных действиях

> [!NOTE]
> См. раздел [ограничения службы подписки для наблюдения](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) за числовыми ограничениями для каждого из указанных ниже элементов.  

### <a name="automation-runbook"></a>Runbook автоматизации
Ограничения на полезные данные Runbook см. в разделе [ограничения службы подписки Azure](../../azure-subscription-service-limits.md) .

У вас может быть ограниченное количество действий Runbook в группе действий. 

### <a name="azure-app-push-notifications"></a>Push-уведомления приложения Azure
У вас может быть ограниченное количество действий в приложении Azure в группе действий.

### <a name="email"></a>Адрес эл. почты
Электронная почта будет отправляться на следующие адреса (правильно настройте фильтрацию электронной почты):
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

У вас может быть ограниченное количество действий электронной почты в группе действий. См. статью [сведения о ограничении скорости](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Роль Azure Resource Manager по электронной почте
Отправьте сообщение электронной почты членам роли подписки.

У вас может быть ограниченное количество действий электронной почты в группе действий. См. статью [сведения о ограничении скорости](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Функция
Функциональные ключи для приложений-функций, настроенных как действия, считываются через API функций, который в настоящее время требует приложений-функций версии 2, чтобы настроить параметр приложения "Азуревебжобссекретсторажетипе" на "Files". Дополнительные сведения см. [в разделе изменения в управлении ключами в функциях версии 2]( https://aka.ms/funcsecrets).

У вас может быть ограниченное число действий функций в группе действий.

### <a name="itsm"></a>ITSM
Действие ITSM требует подключения ITSM. Дополнительные сведения о создании подключения ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).

У вас может быть ограниченное количество действий ITSM в группе действий. 

### <a name="logic-app"></a>Приложение логики
У вас может быть ограниченное количество действий приложения логики в группе действий.

### <a name="secure-webhook"></a>Безопасный веб-перехватчик
**Функция безопасного веб-перехватчика в настоящее время доступна в предварительной версии.**

Действие веб-перехватчика группы действий позволяет воспользоваться преимуществами Azure Active Directory для защиты подключения между группой действий и защищенным веб-API (конечной точкой перехватчика). Ниже описан общий рабочий процесс для использования этой функции. Общие сведения о приложениях и субъектах-службах Azure AD см. в [статье Обзор платформы Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Создайте приложение Azure AD для защищенного веб-API. См. раздел https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Настройте защищенный API, который будет вызываться приложением демона.
    
1. Включите группы действий для использования приложения Azure AD.

    > [!NOTE]
    > Для выполнения этого скрипта необходимо быть членом [роли администратора приложения Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) .
    
    - Измените вызов Connect-AzureAD в сценарии PowerShell, чтобы использовать идентификатор клиента Azure AD.
    - Измените переменную сценария PowerShell $myAzureADApplicationObjectId для использования идентификатора объекта приложения Azure AD.
    - Запустите измененный скрипт.
    
1. Настройка действия "Защита веб-перехватчика группы действий".
    - Скопируйте значение $myApp. ObjectId из скрипта и введите его в поле Идентификатор объекта приложения в определении действия веб-перехватчика.
    
    ![Действие "безопасный веб-перехватчик"](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Безопасный скрипт PowerShell веб-перехватчика

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
Дополнительные важные сведения см. в статье [сведения о ограничении скорости](./../../azure-monitor/platform/alerts-rate-limiting.md) и поведении [оповещений SMS](../../azure-monitor/platform/alerts-sms-behavior.md) .

У вас может быть ограниченное количество действий SMS в группе действий.  

### <a name="voice"></a>Голос
См. статью [сведения о ограничении скорости](./../../azure-monitor/platform/alerts-rate-limiting.md) .

В группе действий может быть ограниченное количество голосовых действий.

### <a name="webhook"></a>Веб-перехватчик
Повторные попытки веб-перехватчиков выполняются с помощью следующих правил. Повторный вызов веб-перехватчика выполняется не более двух раз, когда возвращаются следующие коды состояния HTTP: 408, 429, 503, 504 (или если конечная точка HTTP не отвечает). Первый повторный вызов происходит через 10 секунд, второй — через 100 секунд. После двух сбоев группа действий не будет вызывать конечную точку в течение 30 минут. 

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

Для получения обновлений об изменениях этих IP-адресов рекомендуется настроить оповещение о работоспособности службы, которое отслеживает информационные уведомления о службе групп действий.

У вас может быть ограниченное число действий веб-перехватчика в группе действий.



## <a name="next-steps"></a>Следующие шаги
* Дополнительные сведения о поведении SMS-оповещений в группе действий см. в [этой статье](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Узнайте о [схеме веб-перехватчика для оповещений журнала действий](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Дополнительные сведения о соединителе ITSM см. в статье [Централизованное управление рабочими элементами ITSM с помощью соединителя управления ИТ-службами (предварительная версия)](../../azure-monitor/platform/itsmc-overview.md).
* Дополнительные сведения о лимитах для оповещений см. в статье [Ограничение частоты отправки для SMS, сообщений электронной почты и вызовов Webhook](../../azure-monitor/platform/alerts-rate-limiting.md).
* Изучите [обзор оповещений журнала действий](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о работоспособности службы](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
