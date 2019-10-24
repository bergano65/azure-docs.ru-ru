---
title: Обновление или удаление назначения настраиваемой роли Azure AD в Управление привилегированными пользователями (PIM) | Документация Майкрософт
description: Как обновить или удалить назначения настраиваемой роли Azure AD в Управление привилегированными пользователями (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccbc9fbd763b9393a64d6cfc29f6b5a9d021da88
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756339"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Обновление или удаление назначенной настраиваемой роли Azure AD в Управление привилегированными пользователями

В этой статье объясняется, как использовать управление привилегированными пользователями (PIM) для обновления и удаления привязанного к задачам и ограниченного по времени назначения настраиваемых ролей, созданных для управления приложениями в Azure Active Directory (Azure AD). 

- Дополнительные сведения о создании настраиваемых ролей для делегирования управления приложениями в AAD см. в статье [о настраиваемых ролях администратора в Azure Active Directory (предварительная версия)](../users-groups-roles/roles-custom-overview.md). 
- Если вы еще не использовали управление привилегированными пользователями, получите дополнительные сведения в статье [Начало работы с управлением привилегированными пользователями](pim-getting-started.md).

> [!NOTE]
> Настраиваемые роли AAD в период предварительной версии не интегрируются со встроенными ролями каталога. Как только эта возможность станет общедоступной, управление всеми ролями будет выполняться в интерфейсе для встроенных ролей.

## <a name="update-or-remove-an-assignment"></a>Обновление или удаление назначения

Выполните следующие действия, чтобы обновить или удалить существующее назначение настраиваемой роли.

1. Войдите в раздел [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) на портале Azure с учетной записью пользователя, которому назначена роль "Администратор привилегированных ролей".
1. Щелкните **Настраиваемые роли Azure AD (предварительная версия)** .

    ![Выбор предварительной версии настраиваемых ролей Azure AD для просмотра доступных назначений ролей](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Выберите **Роли**, чтобы просмотреть список **Назначения** настраиваемых ролей для приложений Azure AD.

    ![Выбор раздела "Роли" для просмотра списка допустимых назначений ролей](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Щелкните роль, которую нужно обновить или удалить.
1. Найдите назначение роли на вкладках **Доступные роли** или **Активные роли**.
1. Нажмите кнопку **Обновить** или **Удалить**, чтобы обновить или удалить назначение роли.

    ![Выберите "Удалить" или "Обновить" в назначении допустимой роли](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Активация настраиваемой роли Azure AD](azure-ad-custom-roles-assign.md)
- [Назначение настраиваемой роли Azure AD](azure-ad-custom-roles-assign.md)
- [Настройка назначения настраиваемой роли Azure AD](azure-ad-custom-roles-configure.md)
