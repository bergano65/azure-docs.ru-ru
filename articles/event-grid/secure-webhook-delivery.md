---
title: Безопасная доставка WebHook с Azure AD в azure Event Grid
description: Описывает, как доставлять события в конечные точки HTTPS, защищенные активным каталогом Azure, с помощью Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931552"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Публикация событий в защищенных конечными точками active Directory Azure

В этой статье описывается, как использовать преимущества Active Directory Azure для обеспечения связи между подпиской на события и конечной точкой веб-крючка. Для обзора приложений azure AD и основ [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)службы см.

В этой статье используется портал Azure для демонстрации, однако функция также может быть включена с помощью CLI, PowerShell или SDKs.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Создание приложения Azure AD

Начните с создания приложения Azure AD для защищенной конечных точек. См. раздел https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Наверстримируйте защищенный API, который будет вызываться приложением daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Включить сетку событий для использования приложения Azure AD

Используйте приведенный ниже сценарий PowerShell, чтобы создать принцип роли и обслуживания в приложении Azure AD. Вам понадобится идентификатор клиента и идентификатор объекта из приложения Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Измените $myTenantId скрипта PowerShell, чтобы использовать идентификатор aD Azure AD.
1. Изменение $myAzureADApplicationObjectId скрипта PowerShell для использования идентификатора объекта приложения Azure AD
1. Выполнить измененный скрипт.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Настройка подписки на событие

В потоке создания для подписки на событие выберите тип endpoint 'Web Hook'. После того как вы дали свою конечную точку URI, нажмите на вкладку дополнительных функций в верхней части лезвия подписки на события.

![Выберите веб-крюк типа конечных точек](./media/secure-webhook-delivery/select-webhook.png)

Во вкладке дополнительных функций проверьте поле для проверки подлинности AAD и наймите идентификатор идентификатор клиента:

* Копируйте идентификатор AD-накопителя Azure AD из вывода скрипта и введите его в поле AAD Tenant ID.
* Копируйте идентификатор приложения Azure AD на выходе скрипта и введите его в поле ИДЕНта приложений AAD.

    ![Безопасные действия Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Дополнительные сведения о ключе аутентификации см. в статье [Сетка событий: безопасность и проверка подлинности](security-authentication.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
