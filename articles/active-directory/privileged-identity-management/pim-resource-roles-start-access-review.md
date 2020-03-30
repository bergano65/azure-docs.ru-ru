---
title: Создайте обзор ролей ресурсов Azure в PIM - Azure AD Документы Майкрософт
description: Узнайте, как создать обзор ролей ресурсов Azure в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847072"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Создание обзора ролей ресурсов Azure в привилегированном управлении identity

Доступ к привилегированным функциям ресурсов Azure для сотрудников изменяется с течением времени. Чтобы уменьшить риск, связанный с устаревшими назначениями ролей, следует регулярно просматривать доступ. Можно использовать Azure Active Directory (Azure AD) Privileged Identity Management (PIM) для создания обзоров доступа для привилегированных ролей ресурсов Azure. Вы также можете настроить повторяющиеся отзывы доступа, которые происходят автоматически.

В этой статье описывается, как создать один или несколько обзоров доступа для привилегированных ролей ресурсов Azure.

## <a name="prerequisites"></a>Предварительные требования

[Привилегированный администратор ролей](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Обзоры открытого доступа

1. Войдите на [портал Azure](https://portal.azure.com/) с учетными данными пользователя, который является участником роли Администратор привилегированных ролей.

1. Открытое **управление привилегированным удостоверением личности Azure AD.**

1. В левом меню выберите **ресурсы Azure.**

1. Выберите ресурс, которым вы хотите управлять, например группу по подписке или управлению.

1. Под управлением выберите **обзоры доступа**.

    ![Ресурсы Azure - Список обзоров доступа, показывающий состояние всех отзывов](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Запуск проверки доступа

Завершив настройку параметров для проверки доступа, щелкните **Запустить**. Обзор доступа будет отображаться в вашем списке с показателем его состояния.

![Список обзоров доступа, показывающий состояние начатого обзора](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

По умолчанию Azure AD отправляет электронные сообщения рецензентам вскоре после запуска проверки. Если вы выбрали не отправлять сообщения через службу Azure AD, обязательно сообщите рецензентам, что им необходимо выполнить проверку доступа. Вы можете показать им инструкции по [просмотру доступа к ролям ресурсов Azure.](pim-resource-roles-perform-access-review.md)

## <a name="manage-the-access-review"></a>Управление проверкой доступа

Вы можете отслеживать прогресс по мере того, как рецензенты завершают свои отзывы на странице **Обзора** Обзора Доступа. Права доступа в каталоге не изменяются до [завершения проверки.](pim-resource-roles-complete-access-review.md)

![Страница обзора обзоров доступа, показывающая детали обзора](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Если это одноразовый обзор, то после окончания периода проверки доступа или остановки администратором обзора доступа выполните последующие шаги в [завершении обзора функций ресурсов Azure,](pim-resource-roles-complete-access-review.md) чтобы просмотреть и применить результаты.  

Чтобы управлять серией обзоров доступа, перейдите к обзору доступа, и вы найдете предстоящие случаи в запланированных обзорах, и отспособьте дату окончания или добавить/удалить рецензентов соответственно.

На основе выбора в **настройках завершения,** автоматическое применение будет выполнено после даты окончания обзора или когда вы вручную остановите обзор. Статус обзора будет меняться от **завершенных** через промежуточные состояния, такие как **Applying** и, наконец, к состоянию **Прикладной**. Вы должны ожидать, что удаленные пользователи, если таковые будут удалены, будут удалены из ролей в течение нескольких минут.

## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр доступа к ролям ресурсов Azure](pim-resource-roles-perform-access-review.md)
- [Завершите обзор ролей ресурсов Azure](pim-resource-roles-complete-access-review.md)
- [Создание обзора доступа ролей Azure AD](pim-how-to-start-security-review.md)
