---
title: Роли, которыми вы не можете управлять в PIM в Azure | Документация Майкрософт
description: Сведения о ролях, которыми вы не можете управлять в Azure Active Directory Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 80fbad64cda9267e468f9385d48dd5d40468eaca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203425"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Роли, которыми вы не можете управлять в PIM

Azure AD Privileged Identity Management (PIM) позволяет управлять всеми [ролями каталога Azure AD](../users-groups-roles/directory-assign-admin-roles.md) и всеми [ролями службы ресурсов Azure](../../role-based-access-control/built-in-roles.md). К этим ролям также относятся ваши пользовательские роли, подключенные к группам управления, подпискам, группам ресурсов и ресурсам. Однако, есть также несколько ролей, которыми вы не можете управлять. В этой статье описываются роли, которыми вы не можете управлять в PIM.

## <a name="classic-subscription-administrator-roles"></a>Роли классического администратора подписки

Вы не можете управлять следующими ролями администратора классической подписки в PIM.

- Администратор учетной записи
- Администратор служб
- Соадминистратор

Дополнительные сведения см. в статье [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Сведения о ролях администратора Office 365

За исключением администраторов Exchange и SharePoint, роли в Exchange Online или SharePoint Online не представлены в Azure AD, и поэтому ими нельзя управлять в PIM. Дополнительные сведения об этих службах Office 365 см. в статье [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Администратор SharePoint имеет административный доступ к SharePoint Online с помощью Центра администрирования SharePoint и может выполнить практически любую задачу в SharePoint Online. Допустимые пользователей могут наблюдать задержки при использовании данной роли в SharePoint после активации в PIM.

## <a name="next-steps"></a>Дополнительная информация

- [Начало работы с PIM](pim-getting-started.md)
- [Назначение ролей каталога Azure AD в PIM ](pim-how-to-add-role-to-user.md)
- [Назначение ролей ресурсам Azure в PIM](pim-resource-roles-assign-roles.md)
