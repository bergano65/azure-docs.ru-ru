---
title: Создание проверки доступа Azure AD ролей в PIM — Azure Active Directory | Документация Майкрософт
description: Узнайте, как создать проверку доступа для ролей Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141706"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Создание проверки доступа Azure AD ролей в PIM

Доступ к привилегированные роли Azure AD для сотрудников изменения со временем. Чтобы снизить риск, связанный с назначениями устаревших ролей, следует регулярно проверять доступ. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) можно использовать для создания проверки доступа привилегированных ролей Azure AD. Можно также настроить повторяющиеся проверки доступа, которые выполняются автоматически.

В этой статье описывается, как создать один или несколько проверок доступа привилегированных ролей Azure AD.

## <a name="prerequisites"></a>Технические условия

- [Администратор привилегированных ролей](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Проверки доступа откройте

1. Войдите в [портала Azure](https://portal.azure.com/) с пользователем, который является членом роли Администратор привилегированных ролей.

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. В меню слева щелкните **ролей Azure AD** и нажмите кнопку **проверок доступа**.

1. В разделе Управление, выберите **проверок доступа**.

    ![Проверки доступа Azure AD роли —](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Запуск проверки доступа

Завершив настройку параметров для проверки доступа, щелкните **Запустить**. Проверки доступа будут отображаться в списке с индикатором, о его состоянии.

![Список проверок доступа](./media/pim-how-to-start-security-review/access-reviews-list.png)

По умолчанию Azure AD отправляет электронные сообщения рецензентам вскоре после запуска проверки. Если вы выбрали не отправлять сообщения через службу Azure AD, обязательно сообщите рецензентам, что им необходимо выполнить проверку доступа. Вы можете показать им инструкции по [проверка доступа к роли Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Управление проверкой доступа

Можно отслеживать ход выполнения проверяющими их проверок на **Обзор** страницу проверки доступа. Нет прав доступа, будут изменены в каталоге до [проверка завершается](pim-how-to-complete-review.md).

![Ход выполнения проверки доступа](./media/pim-how-to-start-security-review/access-review-overview.png)

Если это однократной проверки, после окончания периода проверки доступа или администратор остановил проверку, следуйте указаниям [завершение проверки доступа для ролей Azure AD](pim-how-to-complete-review.md) Чтобы просмотреть и применить результаты.  

Для управления ряд доступа обзоры, перейдите на проверку доступа, и вам будет поиска предстоящих в запланированные проверки и измените дату окончания или добавление и удаление рецензентов соответствующим образом.

С учетом выбранных в **после заполнения параметров**, автоматическое применение будет выполняться после окончания проверки или когда вручную остановите проверку. Состояние проверки изменится с **завершено** через несколько промежуточных этапов, такие как **применение** и, наконец состояние **применения**. Отклоненные пользователи должны ожидать, если таковое имеется, удаляемой из роли несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка доступа к роли Azure AD](pim-how-to-perform-security-review.md)
- [Завершение проверки доступа для ролей Azure AD](pim-how-to-complete-review.md)
- [Создание проверки доступа для ролей ресурсов Azure](pim-resource-roles-start-access-review.md)
