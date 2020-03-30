---
title: Назначить Azure AD пользовательскую роль - Привилегированное управление идентификацией (PIM)
description: Сведения о том, как назначать настраиваемую роль AAD через Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499252"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Присвоение настраиваемой роли AAD через Privileged Identity Management (PIM)

В этой статье объясняется, как с помощью Privileged Identity Management (PIM) создать привязанные к задачам и ограниченные по времени назначения настраиваемых ролей, созданных для управления приложениями в административном интерфейсе Azure Active Directory (AAD).

- Дополнительные сведения о создании настраиваемых ролей для делегирования управления приложениями в AAD см. в статье [о настраиваемых ролях администратора в Azure Active Directory (предварительная версия)](../users-groups-roles/roles-custom-overview.md).
- Если вы еще не использовали управление привилегированными пользователями, получите дополнительные сведения в статье [Начало работы с управлением привилегированными пользователями](pim-getting-started.md).
- Для получения информации о том, как предоставить другому администратору доступ к управлению привилегированными удостоверениями, можно [получить доступ к другим администраторам для управления привилегированным управлением идентификацией.](pim-how-to-give-access-to-pim.md)

> [!NOTE]
> Настраиваемые роли AAD в период предварительной версии не интегрируются со встроенными ролями каталога. Как только эта возможность станет общедоступной, управление всеми ролями будет выполняться в интерфейсе для встроенных ролей. Если вы видите следующий баннер, эти роли должны управляться [в встроенных ролей опыт](pim-how-to-activate-role.md) и эта статья не применяется:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Назначение роли

Управление привилегированными пользователями позволяет управлять настраиваемыми ролями, которые вы создаете в интерфейсе управления приложениями в Azure Active Directory (AAD).  Следующие шаги создают допустимое назначение для настраиваемой роли каталога.

1. Войдите в раздел [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) на портале Azure с учетной записью пользователя, которому назначена роль "Администратор привилегированных ролей".
1. Щелкните **Настраиваемые роли Azure AD (предварительная версия)**.

    ![Выбор предварительной версии настраиваемых ролей Azure AD для просмотра доступных назначений ролей](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Выберите **Роли**, чтобы просмотреть список настраиваемых ролей для приложений AAD.

    ![Выбор раздела "Роли" для просмотра списка допустимых назначений ролей](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Щелкните **Добавить участника**, чтобы открыть страницу назначения.
1. Чтобы ограничить область назначения роли отдельным приложением, выберите область приложения в разделе **Область**.

    ![Ограничение области допустимых назначений роли в AAD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Щелкните **Выбрать роль**, чтобы открыть список **Выбор роли**.

    ![Выбор допустимой роль для назначения пользователю](./media/azure-ad-custom-roles-assign/select-role.png)

1. Выберите роль, которую вы намерены назначить, и щелкните **Выбрать**. Откроется список **Выберите участника**.

    ![Выбор участника, которому назначается роль](./media/azure-ad-custom-roles-assign/select-member.png)

1. Щелкните пользователя, которому вы намерены назначить роль, а затем щелкните **Выбрать**. Откроется список **Параметры членства**.

    ![Выбор типа назначения роли: "Допустимое" или "Активное"](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. На странице **Параметры членства** выберите значение **Допустимое** или **Активное**.

    - Назначение **допустимой** роли означает, что пользователь должен выполнить некоторое действие для использования этой роли. Это могут быть такие действия, как прохождение многофакторной проверки подлинности, предоставление коммерческого обоснования или запрос утверждения от назначенных утверждающих.
    - Назначение **активной** роли не требует дополнительных действий для использования роли. Члены с активным назначением роли постоянно имеют все полномочия, присвоенные этой роли.

1. Если флажок **Постоянный** доступен для просмотра и изменения (в зависимости от параметров роли), вы можете сделать назначение постоянным. Если назначение должно быть постоянно активным или постоянно допустимым, установите этот флажок. Снимите флажок, чтобы ограничить длительность назначения.
1. Чтобы создать новое назначение роли, последовательно щелкните **Сохранить** и **Добавить**. Отображается уведомление о состоянии процесса назначения.

Чтобы проверить назначение ролей в открытой роли, выберите **Назначения** > **и** убедитесь, что ваше назначение роли правильно определено как подходящее или активное.

 ![Проверка, отображается ли назначение роли как допустимое или активное](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Активация настраиваемой роли Azure AD](azure-ad-custom-roles-assign.md)
- [Update or remove an assigned Azure AD custom role in Privileged Identity Management](azure-ad-custom-roles-update-remove.md) (Обновление или удаление назначенной настраиваемой роли AAD в Privileged Identity Management)
- [Настройка назначения настраиваемой роли Azure AD](azure-ad-custom-roles-configure.md)
- [Administrator role permissions in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) (Разрешения роли администратора в Azure Active Directory)
