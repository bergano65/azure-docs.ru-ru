---
title: Роли, которыми нельзя управлять в управление привилегированными пользователями-Azure Active Directory | Документация Майкрософт
description: Сведения о ролях, которыми вы не можете управлять в Azure Active Directory Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607520"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Роли, которыми нельзя управлять в управление привилегированными пользователями

Azure Active Directory (Azure AD) управление привилегированными пользователями (PIM) позволяет управлять всеми ролями Azure [AD](../users-groups-roles/directory-assign-admin-roles.md) и всеми [ролями Azure](../../role-based-access-control/built-in-roles.md). Роли Azure также могут включать пользовательские роли, присоединенные к группам управления, подпискам, группам ресурсов и ресурсам. Однако, есть также несколько ролей, которыми вы не можете управлять. В этой статье описываются роли, которыми нельзя управлять в управление привилегированными пользователями.

## <a name="classic-subscription-administrator-roles"></a>Роли классического администратора подписки

Вы не можете управлять следующими ролями администратора классической подписки в управление привилегированными пользователями:

- Администратор учетной записи
- Администратор служб
- Соадминистратор

Дополнительные сведения см. в статье [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Сведения о ролях администратора Office 365

Мы поддерживаем все роли Office 365 на портале ролей и администраторов Azure AD, такие как администратор Exchange и администратор SharePoint, но мы не поддерживаем определенные роли в Exchange RBAC и RBAC в SharePoint. Дополнительные сведения об этих службах Office 365 см. в статье [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Пользователи с правами администратора SharePoint, а также любые роли, пытающиеся получить доступ к центру безопасности и соответствия требованиям Майкрософт, могут столкнуться с задержкой до нескольких часов после активации своей роли. Мы работаем с этими командами для решения проблем.

## <a name="next-steps"></a>Дальнейшие шаги

- [Назначение ролей Azure AD в управление привилегированными пользователями](pim-how-to-add-role-to-user.md)
- [Назначение ролей ресурсов Azure в управление привилегированными пользователями](pim-resource-roles-assign-roles.md)
