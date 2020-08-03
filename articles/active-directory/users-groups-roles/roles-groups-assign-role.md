---
title: Назначение роли для облачной группы в Azure Active Directory | Документация Майкрософт
description: Назначьте роль Azure AD для назначаемой роли группе в портал Azure, PowerShell или API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b5709369dfc2025b55bc3acec69e9328ce403
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513423"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Назначение роли для облачной группы в Azure Active Directory

В этом разделе описывается, как ИТ-администратор может назначить роль Azure Active Directory (Azure AD) группе Azure AD.

## <a name="using-azure-ad-admin-center"></a>Использование центра администрирования Azure AD

Назначение группы роли Azure AD аналогично назначению пользователей и субъектов-служб, за исключением того, что можно использовать только те группы, которые являются назначаемыми ролями. В портал Azure отображаются только те группы, которые являются назначенными ролями.

1. Войдите в [центр администрирования Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) с правами администратора привилегированных ролей или глобального администратора в Организации Azure AD.

1. Выберите **Azure Active Directory**  >  **роли и администраторы**, а затем выберите роль, которую нужно назначить.

1. На странице ***имя роли*** выберите > **добавить назначение**.

   ![Добавление нового назначения роли](./media/roles-groups-assign-role/add-assignment.png)

1. Выберите группу. Отображаются только группы, которые могут быть назначены ролям Azure AD.

    [![Для нового назначения ролей отображаются только доступные для назначения группы.](media/roles-groups-assign-role/eligible-groups.png "Для нового назначения ролей отображаются только доступные для назначения группы.")](media/roles-groups-assign-role/eligible-groups.png#lightbox)

1. Выберите **Добавить**.

Дополнительные сведения о назначении разрешений роли пользователям см. в статье [назначение ролей администратора и пользователей, не являющихся администраторами](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Использование PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Создание группы, которой можно назначить роль

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Получение определения роли, которую вы хотите назначить

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Создание назначения роли

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Использование Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Создание группы, которой можно назначить роль Azure AD

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Получение определения роли

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Создание назначения роли

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Дальнейшие действия

- [Использование облачных групп для управления назначениями ролей](roles-groups-concept.md)
- [Устранение неполадок ролей, назначенных облачным группам](roles-groups-faq-troubleshooting.md)
