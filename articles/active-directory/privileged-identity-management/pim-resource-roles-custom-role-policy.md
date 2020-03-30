---
title: Используйте пользовательские роли для ресурсов Azure в PIM - Azure AD Документы Майкрософт
description: Узнайте, как использовать настраиваемые роли ресурсов Azure в Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847064"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Используйте пользовательские роли для ресурсов Azure в привилегированном управлении идентификацией

Возможно, потребуется применить строгие настройки privileged Identity Management (PIM) для некоторых пользователей, выполняющих привилегированное участие в организации Azure Active Directory (Azure AD), обеспечивая при этом большую автономию для других пользователей. Рассмотрим, например, сценарий, при котором организация нанимает нескольких контрактных партнеров для оказания помощи в разработке приложения, которое будет работать в подписке Azure.

Вам как администратору ресурсов было бы удобно, чтобы сотрудники получали доступ без обязательных подтверждений. Тем не менее, все они должны получать подтверждение при запросе на доступ к ресурсам организации.

Выполните действия, изложенные в следующем разделе, для настройки целевых параметров управления привилегированными удостоверениями для ролей ресурсов Azure.

## <a name="create-the-custom-role"></a>Создание настраиваемой роли

Чтобы создать для ресурса настраиваемую роль, выполните инструкции, описанные в статье [Создание пользовательских ролей для управления доступом на основе ролей в Azure](../role-based-access-control-custom-roles.md).

После создания настраиваемой роли присвойте ей описательное имя, чтобы легко определять, какие из встроенных ролей нужно дублировать.

> [!NOTE]
> Убедитесь, что настраиваемая роль является дубликатом встроенной роли, которую вы хотите дублировать, и что ее область действия соответствует встроенной роли.

## <a name="apply-pim-settings"></a>Применение параметров PIM

После создания роли в организации Azure AD перейдите на страницу **ресурсов Privileged Identity - Azure** на портале Azure. Выберите ресурс, к которому применяется роль.

![Панель "Управление привилегированными пользователями — ресурсы Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Настроили настройки роли privileged Identity Management,](pim-resource-roles-configure-role-settings.md) которые должны применяться к этим членам роли.

Наконец, [назначьте роли](pim-resource-roles-assign-roles.md) определенной группе участников, для которых вы хотите применить настроенные параметры.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка параметров роли ресурсов Azure в привилегированном управлении идентификацией](pim-resource-roles-configure-role-settings.md)
- [Пользовательские роли в Azure](../../role-based-access-control/custom-roles.md)
