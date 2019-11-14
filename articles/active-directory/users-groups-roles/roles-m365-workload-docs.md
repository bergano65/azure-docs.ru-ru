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
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024458"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Роли администратора для служб Microsoft 365 Services

Всеми продуктами в Microsoft 365 можно управлять с помощью административных ролей в Azure AD. Некоторые продукты также предоставляют дополнительные роли, относящиеся к этому продукту. Сведения о ролях, включенных в каждый продукт, см. в следующей таблице. Общие обсуждения проблем делегирования см. в статье [Делегирование администрирования в Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Где найти содержимое

Служба Microsoft 365 | Содержимое роли | API содержимого
---------------------- | ------------------ | -----------------
Роли администратора в бизнес-планах Office 365 и Microsoft 365 | [Роли администраторов в Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Недоступно
Azure Active Directory (Azure AD) и защита удостоверений Azure Active Directory| [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Общие сведения об управлении доступом на основе ролей](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Add-ManagementRoleEntry](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Получение назначений ролей](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md)<br>Также [Роль администратора SharePoint Online](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Команды и Skype для бизнеса | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Центр безопасности и соответствия требованиям (Office 365 Advanced Threat Protection, Exchange Online Protection, защита информации) | [Роли администраторов в Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Add-ManagementRoleEntry](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Получение назначений ролей](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Оценка безопасности | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Диспетчер соответствия | [Разрешения и управление доступом на основе ролей](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Недоступно
Azure Information Protection | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Контроль доступа на основе ролей](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Справочник по API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Расширенная защита от угроз Azure | [Группы ролей Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Недоступно
Advanced Threat Protection в Защитнике Windows | [Управление доступом к порталу с помощью элемента управления доступом на основе ролей](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Недоступно
управление привилегированными пользователями; | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Управление доступом на основе ролей (RBAC) с помощью Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Управляемый рабочий стол | [Разрешения роли администратора в Azure Active Directory](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Получение назначений ролей](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Дополнительная информация

* [Просмотр и назначение ролей администратора в Azure Active Directory](directory-manage-roles-portal.md)
* [Administrator role permissions in Azure Active Directory](directory-assign-admin-roles.md) (Разрешения ролей администратора в Azure Active Directory)
