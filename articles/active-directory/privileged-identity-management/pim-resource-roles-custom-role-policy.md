---
title: Применение настраиваемых ролей ресурсов Azure в PIM | Документация Майкрософт
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
ms.openlocfilehash: f2767d817d6f15d38aeef4c669b2b11c3d8a8a6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168023"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Применение настраиваемых ролей ресурсов Azure в PIM

Иногда для некоторых участников роли нужно применить довольно строгие параметры управления привилегированными пользователями (PIM), сохраняя высокий уровень автономности для других участников этой же роли. Предположим, ваша компания привлекла нескольких консультантов для помощи в разработке приложения, которое будет выполняться в подписке Azure.

Вам как администратору ресурсов было бы удобно, чтобы сотрудники получали доступ без обязательных подтверждений. Тем не менее, все они должны получать подтверждение при запросе на доступ к ресурсам организации.

Выполните описанную в следующем разделе процедуру, чтобы настроить целевые параметры PIM для ролей ресурсов Azure.

## <a name="create-the-custom-role"></a>Создание настраиваемой роли

Чтобы создать для ресурса настраиваемую роль, выполните инструкции, описанные в статье [Создание пользовательских ролей для управления доступом на основе ролей в Azure](../role-based-access-control-custom-roles.md).

После создания настраиваемой роли присвойте ей описательное имя, чтобы легко определять, какие из встроенных ролей нужно дублировать.

> [!NOTE]
> Убедитесь, что настраиваемая роль является дубликатом встроенной роли, которую вы хотите дублировать, и что ее область действия соответствует встроенной роли.

## <a name="apply-pim-settings"></a>Применение параметров PIM

После создания роли клиенте на портале Azure перейдите на панель **Управление привилегированными пользователями — ресурсы Azure**. Выберите ресурс, к которому применяется роль.

![Панель "Управление привилегированными пользователями — ресурсы Azure"](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Настройте параметры PIM](pim-resource-roles-configure-role-settings.md) для выбранных участников этой роли.

Наконец, [назначьте роли](pim-resource-roles-assign-roles.md) определенной группе участников, для которых вы хотите применить настроенные параметры.

## <a name="next-steps"></a>Дополнительная информация

- [Настройка параметров роли ресурсов Azure в PIM](pim-resource-roles-configure-role-settings.md)
- [Пользовательские роли в Azure](../../role-based-access-control/custom-roles.md)
