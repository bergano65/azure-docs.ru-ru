---
title: Откройте для себя ресурсы Azure для управления в PIM - Azure AD Документы Майкрософт
description: Узнайте, как обнаруживать ресурсы Azure и управлять ими с помощью управления привилегированными пользователями (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022890"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Откройте для себя ресурсы Azure для управления привилегированным управлением идентификацией

Используя Azure Active Directory (Azure AD) Privileged Identity Management (PIM), можно улучшить защиту ресурсов Azure. Это полезно для организаций, которые уже используют Privileged Identity Management для защиты ролей Azure AD, а также для владельцев групп управления и подписки, которые стремятся обеспечить безопасность производственных ресурсов.

При первой настройке Privileged Identity Management для ресурсов Azure необходимо обнаружить и выбрать ресурсы для защиты с помощью Privileged Identity Management. Количество ресурсов, которыми можно управлять с помощью Privileged Identity Management, не ограничено. Однако рекомендуется начать с наиболее важных (рабочих) ресурсов.

## <a name="discover-resources"></a>Обнаружение ресурсов

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Открытое **управление привилегированным удостоверением личности Azure AD.**

1. Щелкните **Ресурсы Azure**.

    Если вы впервые используете privileged Identity Management для ресурсов Azure, вы увидите страницу **ресурсов Discover.**

    ![Откройте для себя панель ресурсов без ресурсов, перечисленных для первого опыта](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Если другой администратор в вашей организации уже управляет ресурсами Azure в Privileged Identity Management, вы увидите список ресурсов, которые в настоящее время управляются.

    ![Откройте для себя ресурсы панели список ресурсов, которые в настоящее время управляется](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Выберите **ресурсы Discover** для запуска опыта обнаружения.

    ![Ресурсы панели обнаружения, которыми можно управлять, такие как подписки и группы управления](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. На странице **«Дискавери»** используйте **фильтр состояния ресурсов** ресурсов и тип **ресурса для** фильтрации групп управления или подписок, на которые вы имеете разрешение написать. Удобнее начать с параметра **Все**.

    Вы можете только искать и выбирать ресурсы управления или подписки для управления с помощью Privileged Identity Management. Когда вы управляете группой управления или подпиской в Привилегированном управлении идентификацией, вы также можете управлять ее детскими ресурсами.

1. Выберите флажок рядом с любыми неуправляемыми ресурсами, которыми вы хотите управлять.

1. Выберите **ресурс Управления,** чтобы начать управление выбранными ресурсами.

    > [!NOTE]
    > После управления группой управления или подпиской она не может быть неуправляемой. Это не позволяет другому администратору ресурсов удалять настройки privileged Identity Management.

    ![Панель обнаружения с выбранным ресурсом и выделенным ресурсом Управления](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Если вы видите сообщение для подтверждения посадки выбранного ресурса для управления, выберите **«Да».**

    ![Сообщение, подтверждающее на борт выбранных ресурсов для управления](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка параметров роли ресурсов Azure в привилегированном управлении идентификацией](pim-resource-roles-configure-role-settings.md)
- [Назначать роли ресурсов Azure в привилегированном управлении identity](pim-resource-roles-assign-roles.md)
