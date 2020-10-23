---
title: Просмотр ролей, назначенных группе, в Azure Active Directory | Документация Майкрософт
description: Узнайте, как можно просматривать роли, назначенные группе, с помощью центра администрирования Azure AD. Просмотр групп и назначенных ролей являются разрешениями пользователя по умолчанию.
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
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377536"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Просмотр ролей, назначенных группе, в Azure Active Directory

В этом разделе описывается, как можно просмотреть роли, назначенные группе, с помощью центра администрирования Azure AD. Просмотр групп и назначенных ролей являются разрешениями пользователя по умолчанию.

1. Войдите в [центр администрирования Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) , используя любые учетные данные без прав администратора или администратора.

1. Выберите интересующую Вас группу.

1. Выберите **назначенные роли**. Теперь вы можете увидеть все роли Azure AD, назначенные этой группе.

   ![Просмотреть все роли, назначенные выбранной группе](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Использование PowerShell

### <a name="get-object-id-of-the-group"></a>Получение идентификатора объекта группы

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Просмотр назначения роли группе

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Использование Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Получение идентификатора объекта группы

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Получение назначений ролей группе

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Дальнейшие шаги

- [Использование облачных групп для управления назначениями ролей](groups-concept.md)
- [Устранение неполадок ролей, назначенных облачным группам](groups-faq-troubleshooting.md)
