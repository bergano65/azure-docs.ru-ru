---
title: Использование настраиваемых ролей для ресурсов Azure в PIM — Azure Active Directory | Документация Майкрософт
description: Узнайте, как использовать настраиваемые роли ресурсов Azure в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602057"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Применение настраиваемых ролей ресурсов Azure в PIM

Может потребоваться применить строгие параметры Azure Active Directory (Azure AD) Privileged Identity Management (PIM) некоторых членов роли, при высокий уровень автономности для других пользователей. Предположим, ваша компания привлекла нескольких консультантов для помощи в разработке приложения, которое будет выполняться в подписке Azure.

Вам как администратору ресурсов было бы удобно, чтобы сотрудники получали доступ без обязательных подтверждений. Тем не менее, все они должны получать подтверждение при запросе на доступ к ресурсам организации.

Выполните описанную в следующем разделе процедуру, чтобы настроить целевые параметры PIM для ролей ресурсов Azure.

## <a name="create-the-custom-role"></a>Создание настраиваемой роли

Чтобы создать для ресурса настраиваемую роль, выполните инструкции, описанные в статье [Создание пользовательских ролей для управления доступом на основе ролей в Azure](../role-based-access-control-custom-roles.md).

После создания настраиваемой роли присвойте ей описательное имя, чтобы легко определять, какие из встроенных ролей нужно дублировать.

> [!NOTE]
> Убедитесь, что настраиваемая роль является дубликатом встроенной роли, которую вы хотите дублировать, и что ее область действия соответствует встроенной роли.

## <a name="apply-pim-settings"></a>Применение параметров PIM

После создания роли клиенте на портале Azure перейдите на панель **Управление привилегированными пользователями — ресурсы Azure**. Выберите ресурс, к которому применяется роль.

![Панель "Управление привилегированными пользователями — ресурсы Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Настройте параметры PIM](pim-resource-roles-configure-role-settings.md) для выбранных участников этой роли.

Наконец, [назначьте роли](pim-resource-roles-assign-roles.md) определенной группе участников, для которых вы хотите применить настроенные параметры.

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка параметров роли ресурсов Azure в PIM](pim-resource-roles-configure-role-settings.md)
- [Пользовательские роли в Azure](../../role-based-access-control/custom-roles.md)
