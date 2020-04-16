---
title: Назначать и перечислять роли с областью административного блока (предварительный просмотр) - Активный каталог Azure Документы Майкрософт
description: Использование административных единиц для ограничения сферы назначений ролей в Active Directory Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: a518ff070ea021726ee382169e87cca55cad36d0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428255"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Присваивая функции сферы деятельности административной единице

В Active Directory Azure (Azure AD) можно назначить пользователям роль Azure AD с областью, ограниченной одним или несколькими административными единицами (AUs) для более детального административного контроля.

Для шагов, готовых к использованию PowerShell и Microsoft Graph для управления административными единицами, [см.](roles-admin-units-manage.md#getting-started)

## <a name="roles-available"></a>Роли доступны

Роль  |  Описание
----- |  -----------
Администратор проверки подлинности  |  Имеет доступ к информации о методе проверки подлинности для любого пользователя, не являваемого аниме, только в назначенном административном блоке.
Администратор групп  |  Можно управлять всеми аспектами параметров групп и групп, такими как именование и политики истечения срока действия только в назначенной административной единице.
Администратор службы технической поддержки  |  Можно сбросить пароли только для администраторов, не являемых администраторами, не администраторами и администраторами службы поддержки в назначенном административном подразделении.
Администратор лицензий  |  Может назначать, удалять и обновлять назначения лицензий только в административном блоке.
администратора паролей.  |  Можно сбросить пароли только для администраторов, не являемых администраторами, не администраторами, в пределах назначенного административного подразделения.
администратора пользователей;  |  Можно управлять всеми аспектами пользователей и групп, включая сброс паролей для ограниченных админ-аминов только в назначенном административном подразделении.

## <a name="assign-a-scoped-role"></a>Назначить масштабную роль

### <a name="azure-portal"></a>Портал Azure

Перейти к **Azure AD > административных единиц** на портале. Выберите административное подразделение, над которым требуется назначить роль пользователю. На левой панели выберите роли и администраторов, чтобы перечислить все доступные роли.

![Выберите административное подразделение для изменения сферы действия роли](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Выберите роль, которая будет назначена, а затем выберите **Добавление назначений.** Это будет слайд открыть панель справа, где вы можете выбрать один или несколько пользователей, которые будут назначены на роль.

![Выберите роль в области, а затем выберите Добавление назначений](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

Выделенный раздел может быть изменен по мере необходимости для конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Перечислите объемные админы в АС

### <a name="azure-portal"></a>Портал Azure

Все назначения ролей, выполненные с областью административного блока, можно просмотреть в [разделе Административных единиц Azure AD.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) Перейти к **Azure AD > административных единиц** на портале. Выберите единицу админ для назначений ролей, которые вы хотите перечислить. Выберите **роли и администраторов** и откройте роль для просмотра назначений в блоке администратора.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

Выделенный раздел может быть изменен по мере необходимости для конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Дальнейшие действия

- [Административные подразделения устранения неполадок и часто задаваемые вопросы](roles-admin-units-faq-troubleshoot.md)
