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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895202"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Роли, которыми нельзя управлять в управление привилегированными пользователями

Azure Active Directory (Azure AD) управление привилегированными пользователями (PIM) позволяет управлять всеми [ролями Azure AD](../users-groups-roles/directory-assign-admin-roles.md) и всеми [ролями ресурсов Azure](../../role-based-access-control/built-in-roles.md). К этим ролям также относятся ваши пользовательские роли, подключенные к группам управления, подпискам, группам ресурсов и ресурсам. Однако, есть также несколько ролей, которыми вы не можете управлять. В этой статье описываются роли, которыми нельзя управлять в управление привилегированными пользователями.

## <a name="classic-subscription-administrator-roles"></a>Роли классического администратора подписки

Вы не можете управлять следующими ролями администратора классической подписки в управление привилегированными пользователями:

- Администратор учетной записи
- Администратор служб
- Соадминистратор

Дополнительные сведения см. в статье [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Сведения о ролях администратора Office 365

Роли в Exchange Online или SharePoint Online, за исключением администратора Exchange и администратора SharePoint, не представлены в Azure AD и поэтому не могут управляться в управление привилегированными пользователями. Дополнительные сведения об этих службах Office 365 см. в статье [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Администратор SharePoint имеет административный доступ к SharePoint Online с помощью Центра администрирования SharePoint и может выполнить практически любую задачу в SharePoint Online. После активации в управление привилегированными пользователями допустимые пользователи могут столкнуться с задержками с помощью этой роли в SharePoint.

## <a name="next-steps"></a>Дальнейшие действия

- [Назначение ролей Azure AD в управление привилегированными пользователями](pim-how-to-add-role-to-user.md)
- [Назначение ролей ресурсов Azure в управление привилегированными пользователями](pim-resource-roles-assign-roles.md)
