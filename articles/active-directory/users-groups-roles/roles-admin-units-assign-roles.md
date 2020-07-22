---
title: Назначение ролей и их перечисление с помощью области административной единицы (Предварительная версия) — Azure Active Directory | Документация Майкрософт
description: Использование административных единиц для ограничения области назначений ролей в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578fb481ec858e65ede49bdce2d8bc26470aa2ca
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850758"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Назначение ролей с заданной областью административной единице

В Azure Active Directory (Azure AD) можно назначить пользователей роли Azure AD с областью действия ограничено одним или несколькими административными единицами (Au) для более детального административного управления.

Инструкции по подготовке к использованию PowerShell и Microsoft Graph для управления административными единицами см. в разделе [Начало работы](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Доступные роли

Роль  |  Описание
----- |  -----------
Администратор проверки подлинности  |  Имеет доступ для просмотра, настройки и сброса сведений о методах проверки подлинности для любого пользователя без прав администратора в назначенной административной единице.
Администратор групп  |  Может управлять всеми аспектами групп и групп, таких как политики именования и истечения срока действия, только в назначенной административной единице.
Администратор службы технической поддержки  |  Может сбрасывать пароли для администраторов без прав администратора и службы технической поддержки только в назначенной административной единице.
Администратор лицензий  |  Может назначать, удалять и обновлять назначения лицензий только в административной единице.
администратора паролей.  |  Может сбрасывать пароли для администраторов без прав администратора и паролей только в назначенной административной единице.
администратора пользователей;  |  Может управлять всеми аспектами пользователей и групп, включая сброс паролей для ограниченных администраторов в пределах назначенной административной единицы.

## <a name="assign-a-scoped-role"></a>Назначение роли с областью действия

### <a name="azure-portal"></a>Портал Azure

Перейдите на портале в раздел **Azure AD > Административные единицы**. Выберите административную единицу, для которой необходимо назначить роль пользователю. В области слева выберите роли и администраторы, чтобы получить список всех доступных ролей.

![Выберите административную единицу для изменения области роли](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Выберите роль, которую необходимо назначить, а затем щелкните **добавить назначения**. На этом слайде откроется панель справа, где можно выбрать одного или нескольких пользователей, которым будет назначена роль.

![Выберите роль в области, а затем щелкните Добавить назначения.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
$uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo
```

Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Вывод списка администраторов с заданной областью в AU

### <a name="azure-portal"></a>Портал Azure

Все назначения ролей, выполненные с помощью области административной единицы, можно просмотреть в [разделе административные единицы в Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Перейдите на портале в раздел **Azure AD > Административные единицы**. Выберите административную единицу для назначений ролей, которые необходимо перечислить. Выберите **роли и администраторы** и откройте роль, чтобы просмотреть назначения в административной единице.

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *
```

Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок и часто задаваемые вопросы об административных единицах](roles-admin-units-faq-troubleshoot.md)
