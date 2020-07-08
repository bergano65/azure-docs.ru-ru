---
title: Создание проверки доступа для ролей Azure AD в PIM — Azure AD | Документация Майкрософт
description: Узнайте, как создать проверку доступа к ролям Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b3070a1296b368ea2c56038ec696942416a6fe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743887"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Создание проверки доступа для ролей Azure AD в управление привилегированными пользователями

Чтобы снизить риск, связанный с устаревшими назначениями ролей, следует регулярно проверять доступ. Вы можете использовать Azure AD Privileged Identity Management (PIM) для создания проверок доступа для привилегированных ролей Azure AD. Также можно настроить автоматические проверки доступа, которые выполняются автоматически.

В этой статье описывается, как создать одну или несколько проверок доступа для привилегированных ролей Azure AD.

## <a name="prerequisites"></a>Предварительные условия

[Администратор привилегированных ролей](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Открытие проверок доступа

1. Войдите в [портал Azure](https://portal.azure.com/) с помощью пользователя, который является членом роли администратора привилегированных ролей.

1. Откройте **Azure AD privileged Identity Management**.

1. Выберите **роли Azure AD**.

1. В разделе Управление выберите проверки **доступа**и нажмите кнопку **создать**.

    ![Роли Azure AD — список проверок доступа, отображающий состояние всех проверок](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Запуск проверки доступа

После указания параметров для проверки доступа нажмите кнопку **запустить**. Проверка доступа будет отображаться в списке с индикатором состояния.

![Список проверок доступа, отображающий состояние запущенных просмотров](./media/pim-how-to-start-security-review/access-reviews-list.png)

По умолчанию Azure AD отправляет электронные сообщения рецензентам вскоре после запуска проверки. Если вы выбрали не отправлять сообщения через службу Azure AD, обязательно сообщите рецензентам, что им необходимо выполнить проверку доступа. Вы можете просмотреть инструкции по [просмотру доступа к ролям Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Управление проверкой доступа

Ход выполнения проверок можно отслеживать на странице **Обзор** проверки доступа. Права доступа не изменяются в каталоге до [завершения проверки](pim-how-to-complete-review.md).

![Страница "Обзор проверок доступа" с подробными сведениями о проверке](./media/pim-how-to-start-security-review/access-review-overview.png)

Если это одноразовая проверка, то после выполнения периода проверки доступа или после остановки проверки доступа администратор должен выполнить действия, описанные в статье [Завершение проверки доступа ролей Azure AD](pim-how-to-complete-review.md) для просмотра и применения результатов.  

Чтобы управлять серией проверок доступа, перейдите к проверке доступа, и вы увидите предстоящие вхождения в плановых проверках и измените дату окончания или добавьте или удалите проверяющих.

В зависимости от сделанного выбора в **параметрах завершения**, автоматическое применение будет выполняться после даты окончания проверки или при ручной отмене проверки. Состояние проверки будет изменено с **завершено** на промежуточные состояния, такие как **применение** и, наконец, к **примененному**состоянию. Вы должны видеть запрещенных пользователей (если они есть), которые удаляются из ролей через несколько минут.

## <a name="next-steps"></a>Дальнейшие шаги

- [Проверка доступа к ролям Azure AD](pim-how-to-perform-security-review.md)
- [Выполнение проверки доступа для ролей Azure AD](pim-how-to-complete-review.md)
- [Создание проверки доступа для ролей ресурсов Azure](pim-resource-roles-start-access-review.md)
