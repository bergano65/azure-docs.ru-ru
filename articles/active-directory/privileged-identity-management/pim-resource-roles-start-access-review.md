---
title: Создание проверки доступа для ролей ресурсов Azure в PIM — Azure Active Directory | Документация Майкрософт
description: Сведения о создании проверки доступа для ролей ресурсов Azure в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0067bd6dc2f47c5460220295d486910d9195782d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476267"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Создание проверки доступа для ролей ресурсов Azure в PIM

Доступ к привилегированному ресурсу Azure ролям для сотрудников, меняется со временем. Чтобы снизить риск, связанный с назначениями устаревших ролей, следует регулярно проверять доступ. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) можно использовать для создания проверки доступа привилегированных ролей ресурсов Azure. Можно также настроить повторяющиеся проверки доступа, которые выполняются автоматически.

В этой статье описывается, как создать один или несколько проверок доступа для ролей ресурсов привилегированными пользователями Azure.

## <a name="prerequisites"></a>Технические условия

- [Администратор привилегированных ролей](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Проверки доступа откройте

1. Войдите в [портала Azure](https://portal.azure.com/) с пользователем, который является членом роли Администратор привилегированных ролей.

1. Откройте страницу **Azure AD Privileged Identity Management**.

1. В меню слева щелкните **ресурсы Azure**.

1. Выберите ресурс, которым нужно управлять, например группу управления или подписку.

1. В разделе Управление, выберите **проверок доступа**.

    ![Ресурсов Azure — список, отображающий состояние всех проверок проверки доступа](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Запуск проверки доступа

Завершив настройку параметров для проверки доступа, щелкните **Запустить**. Проверки доступа будут отображаться в списке с индикатором, о его состоянии.

![Список, отображающий состояние работы Просмотр проверки доступа](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

По умолчанию Azure AD отправляет электронные сообщения рецензентам вскоре после запуска проверки. Если вы выбрали не отправлять сообщения через службу Azure AD, обязательно сообщите рецензентам, что им необходимо выполнить проверку доступа. Вы можете показать им инструкции по [проверку доступа для ролей ресурсов Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Управление проверкой доступа

Можно отслеживать ход выполнения проверяющими их проверок на **Обзор** страницу проверки доступа. Нет прав доступа, будут изменены в каталоге до [проверка завершается](pim-resource-roles-complete-access-review.md).

![Отображение сведений о проверке страницу обзор проверки доступа](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Если это однократной проверки, после окончания периода проверки доступа или администратор остановил проверку, следуйте указаниям [завершение проверки доступа для ролей ресурсов Azure](pim-resource-roles-complete-access-review.md) Чтобы просмотреть и применить результаты.  

Для управления ряд доступа обзоры, перейдите на проверку доступа, и вам будет поиска предстоящих в запланированные проверки и измените дату окончания или добавление и удаление рецензентов соответствующим образом.

С учетом выбранных в **после заполнения параметров**, автоматическое применение будет выполняться после окончания проверки или когда вручную остановите проверку. Состояние проверки изменится с **завершено** через несколько промежуточных этапов, такие как **применение** и, наконец состояние **применения**. Отклоненные пользователи должны ожидать, если таковое имеется, удаляемой из роли несколько минут.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка доступа для ролей ресурсов Azure](pim-resource-roles-perform-access-review.md)
- [Завершение проверки доступа для ролей ресурсов Azure](pim-resource-roles-complete-access-review.md)
- [Создание проверки доступа для ролей Azure AD](pim-how-to-start-security-review.md)
