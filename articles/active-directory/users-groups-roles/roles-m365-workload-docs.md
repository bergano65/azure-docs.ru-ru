---
title: Документация по ролям администраторов в Microsoft 365 службах Azure AD | Документация Майкрософт
description: Поиск содержимого и справочников по API для ролей администратора служб Microsoft 365 в Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6060bc286024183661024abd56567eeefed76430
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053554"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Роли администратора для служб Microsoft 365 Services

Всеми продуктами в Microsoft 365 можно управлять с помощью административных ролей в Azure AD. Некоторые продукты также предоставляют дополнительные роли, относящиеся к этому продукту. Сведения о ролях, включенных в каждый продукт, см. в следующей таблице. Общие обсуждения проблем делегирования см. в статье [Делегирование администрирования в Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Где найти содержимое

Служба Microsoft 365 | Содержимое роли | API содержимого
---------------------- | ------------------ | -----------------
Роли администратора в бизнес-планах Office 365 и Microsoft 365 | [Роли администратора Microsoft 365](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Недоступно
Azure Active Directory (Azure AD) и защита удостоверений Azure Active Directory| [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Общие сведения об управлении доступом на основе ролей](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Add-ManagementRoleEntry](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Получение назначений ролей](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md)<br>Также [о роли администратора SharePoint в Microsoft 365](/sharepoint/sharepoint-admin-role) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Команды и Skype для бизнеса | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Центр безопасности и соответствия требованиям (Office 365 Advanced Threat Protection, Exchange Online Protection, защита информации) | [Роли администраторов в Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [PowerShell для Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Получение назначений ролей](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Оценка безопасности | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Диспетчер соответствия требованиям | [Разрешения и управление доступом на основе ролей](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Недоступно
Azure Information Protection | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Управление доступом на основе ролей](/cloud-app-security/manage-admins) | [Справочник по API](/cloud-app-security/api-tokens) 
Расширенная защита от угроз Azure | [Группы ролей Azure ATP](/azure-advanced-threat-protection/atp-role-groups) | Недоступно
Advanced Threat Protection в Защитнике Windows | [Управление доступом к порталу с помощью элемента управления доступом на основе ролей](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Недоступно
Управление привилегированными пользователями (PIM) | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Управление доступом на основе ролей (RBAC) с помощью Microsoft Intune](/intune/role-based-access-control) | [API Graph](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Получение назначений ролей](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Управляемый рабочий стол | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [API Graph](/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Дальнейшие действия

* [Просмотр и назначение ролей администратора в Azure Active Directory](directory-manage-roles-portal.md)
* [Administrator role permissions in Azure Active Directory](directory-assign-admin-roles.md) (Разрешения ролей администратора в Azure Active Directory)