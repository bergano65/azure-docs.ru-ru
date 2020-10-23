---
title: Создание группы для назначения ролей в Azure Active Directory | Документация Майкрософт
description: Узнайте, как создать назначаемую ролью группу в Azure AD. Управляйте ролями Azure на портале Azure, в PowerShell или API Graph.
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
ms.openlocfilehash: f58aaecaa9f8606399b40fdb1d38a274117ec6a2
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377641"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Создание назначаемой роли группы в Azure Active Directory

Роль можно назначить только группе, которая была создана со свойством "Исассигнаблетороле", установленным в значение true, или созданным на портале Azure AD с **ролями Azure AD, которые могут быть назначены для** включения в группу. Этот атрибут группы делает группу, которая может быть назначена роли в Azure Active Directory (Azure AD). В этой статье описывается, как создать этот особый тип группы. **Примечание.** Группа со свойством Исассигнаблетороле, имеющим значение true, не может иметь динамический тип членства. Дополнительные сведения см. в разделе [Использование группы для управления назначениями ролей Azure AD](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Использование центра администрирования Azure AD

1. Войдите в [центр администрирования Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) с правами администратора привилегированных ролей или глобального администратора в Организации Azure AD.
1. Выберите **группы**  >  **все группы**  >  **Новая группа**.

    [![Откройте Azure Active Directory и создайте новую группу.](./media/groups-create-eligible/new-group.png "Откройте Azure Active Directory и создайте новую группу.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. На вкладке **Новая группа** укажите тип группы, имя и описание.
1. Включить **роли Azure AD можно в группе**. Этот параметр доступен только администраторам привилегированных ролей и глобальным администраторам, так как это только две роли, которые могут задавать параметр.

    [![Сделать новую группу доступной для назначения ролей](./media/groups-create-eligible/eligible-switch.png "Сделать новую группу доступной для назначения ролей")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Выберите участников и владельцев для группы. Вы также можете назначить роли группе, но здесь назначение роли не требуется.

    [![Добавьте участников в группу ролей и назначьте роли.](./media/groups-create-eligible/specify-members.png "Добавьте участников в группу ролей и назначьте роли.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. После того как указаны элементы и владельцы, выберите **создать**.

    [![Кнопка создать находится в нижней части страницы.](./media/groups-create-eligible/create-button.png "Кнопка создать находится в нижней части страницы.")](./media/groups-create-eligible/create-button.png#<lightbox>)

Группа создается с любыми ролями, которые могли быть назначены ей.

## <a name="using-powershell"></a>Использование PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Установка модуля предварительной версии Azure AD

```powershell
install-module azureadpreview
import-module azureadpreview
```

Чтобы убедиться, что модуль готов к использованию, выполните следующую команду:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Создание группы, которой можно назначить роль

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Для группы этого типа всегда будет `isPublic` иметь значение false и `isSecurityEnabled` всегда будет иметь значение true.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Копирование пользователей и субъектов-служб одной группы в группу, назначаемую ролью

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Использование Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Создание назначаемой роли группы в Azure AD

```powershell
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

Для группы этого типа всегда будет `isPublic` иметь значение false и `isSecurityEnabled` всегда будет иметь значение true.

## <a name="next-steps"></a>Дальнейшие шаги

- [Назначение роли для облачной группы](groups-assign-role.md)
- [Использование облачных групп для управления назначениями ролей](groups-concept.md)
- [Устранение неполадок ролей, назначенных облачным группам](groups-faq-troubleshooting.md)
