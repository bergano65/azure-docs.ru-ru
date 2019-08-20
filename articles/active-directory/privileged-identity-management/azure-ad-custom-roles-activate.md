---
title: Активация настраиваемой роли AAD через Privileged Identity Management (PIM) | Документация Майкрософт
description: Сведения о том, как активировать настраиваемую роль AAD через Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e38b0f4463c2188f0b2da6ebb1b57d08af69b41f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947510"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Активация настраиваемой роли AAD через Privileged Identity Management

Privileged Identity Management (PIM) в Azure Active Directory (AAD) теперь поддерживает привязанные к задачам и ограниченные по времени назначения настраиваемых ролей, созданных для управления приложениями в административном интерфейсе управления пользователями и доступом. Дополнительные сведения о создании настраиваемых ролей для делегирования управления приложениями в AAD см. в статье [о настраиваемых ролях администратора в Azure Active Directory (предварительная версия)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Настраиваемые роли AAD в период предварительной версии не интегрируются со встроенными ролями каталога. Как только эта возможность станет общедоступной, управление всеми ролями будет выполняться в интерфейсе для встроенных ролей.

## <a name="activate-a-role"></a>Активация роли

Когда вам потребуется активировать настраиваемую роль AAD, вы можете запросить ее через элемент навигации "Мои роли" в PIM.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Откройте страницу Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Выберите **Azure AD custom roles** (Настраиваемые роли AAD) для просмотра доступных назначений настраиваемых ролей AAD.

   ![Просмотр списка допустимых назначений настраиваемых ролей AAD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

1. На странице **Azure AD custom roles (Preview)** (Настраиваемые роли AAD (предварительная версия)) найдите нужное назначение.
1. Щелкните **Активируйте роль**, чтобы открыть страницу **Активация**.
1. Если эта роль требует многофакторной проверки подлинности, щелкните **Чтобы продолжить, подтвердите свою личность**. Проверка подлинности требуется лишь один раз за сеанс.
1. Щелкните **Подтверждение вашей личности** и следуйте инструкциям по прохождению дополнительной проверки безопасности.
1. Чтобы указать настраиваемую область приложения, выберите **Область**, чтобы открыть панель фильтра. При доступе к роли следует запрашивать минимально необходимую область. Если назначение оформлено для области приложения, вы сможете активировать его только в этой области.

   ![Назначение ресурса AAD в качестве области действия для назначения роли](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Если нужно, укажите время начала активации. Если используется этот параметр, участник роли активируется строго в указанное время.
1. В поле **Причина** введите основание для запроса активации. Их можно сделать обязательными в параметрах роли.
1. Выберите **Активировать**.

Если роль не требует утверждения, она будет активирована в соответствии с указанными настройками и добавлена в список активных ролей. Если вы хотите использовать активированную роль, начните с процедуры [назначения настраиваемой роли Azure AD через Privileged Identity Management](azure-ad-custom-roles-assign.md).

Если роль требует утверждения для вы получите уведомление Azure о том, что запрос ожидает утверждения.

## <a name="next-steps"></a>Дополнительная информация

- [Назначение настраиваемой роли Azure AD](azure-ad-custom-roles-assign.md)
- [Update or remove an assigned Azure AD custom role in Privileged Identity Management](azure-ad-custom-roles-update-remove.md) (Обновление или удаление назначенной настраиваемой роли AAD в Privileged Identity Management)
- [Configure Azure AD custom roles in Privileged Identity Management](azure-ad-custom-roles-configure.md) (Настройка настраиваемых ролей AAD в Privileged Identity Management)
- [Administrator role permissions in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) (Разрешения роли администратора в Azure Active Directory)
