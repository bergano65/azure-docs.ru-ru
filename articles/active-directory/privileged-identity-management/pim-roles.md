---
title: Роли, которые вы не можете управлять в Привилегированном управлении идентификацией - Активный каталог Azure Документы Майкрософт
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
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607520"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Роли, которые нельзя управлять в привилегированном управлении идентификацией

Функциональное каталог Azure (Azure AD) Привилегированное управление идентификацией (PIM) позволяет управлять всеми [ролями Azure AD](../users-groups-roles/directory-assign-admin-roles.md) и всеми [ролями Azure.](../../role-based-access-control/built-in-roles.md) Роли Azure также могут включать пользовательские роли, прилагаемые к группам управления, подпискам, группам ресурсов и ресурсам. Однако, есть также несколько ролей, которыми вы не можете управлять. В этой статье описаны роли, которые нельзя управлять в Привилегированном управлении идентификацией.

## <a name="classic-subscription-administrator-roles"></a>Роли классического администратора подписки

Вы не можете управлять следующими классическими ролями администратора подписки в Privileged Identity Management:

- Администратор учетной записи
- Администратор служб
- Соадминистратор

Дополнительные сведения см. в статье [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Сведения о ролях администратора Office 365

Мы поддерживаем все роли Office365 в опыте портала ролей и администраторов Azure AD, таких как администратор Exchange и администратор SharePoint, но не поддерживаем определенные роли в Exchange RBAC или SharePoint RBAC. Дополнительные сведения об этих службах Office 365 см. в статье [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Подходящие пользователи для роли администратора SharePoint, а также любые роли, пытающиеся получить доступ к Центру безопасности и соответствия Майкрософт, могут испытывать задержки до нескольких часов после активации их роли. Мы работаем с этими командами, чтобы исправить проблемы.

## <a name="next-steps"></a>Следующие шаги

- [Назначать функции Azure AD в привилегированном управлении идентификацией](pim-how-to-add-role-to-user.md)
- [Назначать роли ресурсов Azure в привилегированном управлении identity](pim-resource-roles-assign-roles.md)
