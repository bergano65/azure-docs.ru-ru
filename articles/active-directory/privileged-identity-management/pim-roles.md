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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895202"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Роли, которые нельзя управлять в привилегированном управлении идентификацией

Функциональное управление идентификаторами Azure Active Directory (Azure AD) Позволяет управлять всеми [ролями Azure AD](../users-groups-roles/directory-assign-admin-roles.md) и всеми [функциями ресурсов Azure.](../../role-based-access-control/built-in-roles.md) К этим ролям также относятся ваши пользовательские роли, подключенные к группам управления, подпискам, группам ресурсов и ресурсам. Однако, есть также несколько ролей, которыми вы не можете управлять. В этой статье описаны роли, которые вы не можете управлять в Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Роли классического администратора подписки

Вы не можете управлять следующими классическими ролями администратора подписки в Privileged Identity Management:

- Администратор учетной записи
- Администратор служб
- Соадминистратор

Дополнительные сведения см. в статье [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Сведения о ролях администратора Office 365

Роли в Exchange Online или SharePoint Online, за исключением администратора Exchange и администратора SharePoint, не представлены в Azure AD и поэтому не могут управляться в Privileged Identity Management. Дополнительные сведения об этих службах Office 365 см. в статье [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Администратор SharePoint имеет административный доступ к SharePoint Online с помощью Центра администрирования SharePoint и может выполнить практически любую задачу в SharePoint Online. Подходящие пользователи могут испытывать задержки с использованием этой роли в SharePoint после активации в Привилегированном управлении идентификацией.

## <a name="next-steps"></a>Дальнейшие действия

- [Назначать функции Azure AD в привилегированном управлении идентификацией](pim-how-to-add-role-to-user.md)
- [Назначать роли ресурсов Azure в привилегированном управлении identity](pim-resource-roles-assign-roles.md)
