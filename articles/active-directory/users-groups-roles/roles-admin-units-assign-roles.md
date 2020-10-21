---
title: Назначение ролей и их перечисление с помощью области административной единицы — Azure Active Directory | Документация Майкрософт
description: Использование административных единиц для ограничения области назначений ролей в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d60d803fb73a0f299b00179e5f598f3189e03e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309665"
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

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Субъекты безопасности, которые могут быть назначены роли с областью действия

Следующие субъекты безопасности могут быть назначены роли с областью административной единицы.

* Пользователи
* Назначаемые облачные группы ролей (Предварительная версия)
* Имя субъекта-службы (SPN)

## <a name="assign-a-scoped-role"></a>Назначение роли с областью действия

### <a name="azure-portal"></a>Портал Azure

Перейдите на портале в раздел **Azure AD > Административные единицы**. Выберите административную единицу, для которой необходимо назначить роль пользователю. В области слева выберите роли и администраторы, чтобы получить список всех доступных ролей.

![Выберите административную единицу для изменения области роли](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Выберите роль, которую необходимо назначить, а затем щелкните **добавить назначения**. Откроется панель справа, где можно выбрать одного или нескольких пользователей, которым будет назначена роль.

![Выберите роль в области, а затем щелкните Добавить назначения.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Чтобы назначить роль административной единицы с помощью PIM, выполните действия, описанные [здесь](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
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
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Выделенную часть можно изменить в соответствии с требованиями конкретной среды.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Использование облачных групп для управления назначениями ролей](roles-groups-concept.md)
- [Устранение неполадок ролей, назначенных облачным группам](roles-groups-faq-troubleshooting.md)