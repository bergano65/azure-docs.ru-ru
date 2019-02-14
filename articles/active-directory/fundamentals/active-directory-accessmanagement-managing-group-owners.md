---
title: Добавление или удаление владельцев группы в Azure Active Directory | Документация Майкрософт
description: Инструкции по добавлению и удалению владельцев группы в Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2541a1d76b56f92b250fb422951769db7877213e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181751"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Добавление или удаление владельцев группы в Azure Active Directory
Группы Azure Active Directory (Azure AD) управляются владельцами группы. Владельцы группы назначаются для управления группой и ее участниками владельцем ресурса (администратором). Владельцы группы не должны быть участниками группы. После назначения владельца группы только владелец ресурса можно добавить или удалить владельца.

В некоторых случаях администратор может не назначать владельца группы. В этом случае вы становитесь владельцем группы. Кроме того, владелец может назначать других владельцев группы, если это не запрещено в параметрах группы.

## <a name="add-an-owner-to-a-group"></a>Добавление владельца группы
Добавьте дополнительных владельцев группы в Azure AD.

### <a name="to-add-a-group-owner"></a>Добавление владельца группы
1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью глобального администратора каталога.

2. Выберите **Azure Active Directory**, затем **Группы** и выберите группу, для которой вы хотите добавить владельца (в этом примере — *Политика управления мобильными устройствами — Запад*).

3. На странице обзора группы **Политика управления мобильными устройствами — Запад** выберите **Владельцы**.

    ![Страница обзора группы "Политика управления мобильными устройствами — Запад" с выделенным параметром "Владельцы"](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. На странице **Политика управления мобильными устройствами — Запад — Владельцы** выберите **Добавить владельцев**, а затем найдите и выберите пользователя, который станет новым владельцем группы, и нажмите **Выбрать**.

    ![Страница "Политика управления мобильными устройствами — Запад — Владельцы" с выделенным параметром "Добавить владельцев"](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    После выбора нового владельца обновите страницу **Владельцы** — в списке владельцев появится новое имя.

## <a name="remove-an-owner-from-a-group"></a>Удаление владельца группы
Удаление владельца группы в Azure AD.

### <a name="to-remove-an-owner"></a>Удаление владельца
1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью глобального администратора каталога.

2. Выберите **Azure Active Directory**, затем **Группы** и выберите группу, для которой вы хотите удалить владельца (в этом примере — *Политика управления мобильными устройствами — Запад*).

3. На странице обзора группы **Политика управления мобильными устройствами — Запад** выберите **Владельцы**.

    ![Страница обзора группы "Политика управления мобильными устройствами — Запад" с выделенным параметром "Владельцы"](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. На странице **Политика управления мобильными устройствами — Запад — Владельцы** выберите пользователя, которого необходимо удалить из владельцев группы. Нажмите **Удалить** на странице сведений о пользователе и выберите **Да** для подтверждения.

    ![Страница сведений о пользователе с выделенным параметром "Удалить"](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    После удаления владельца вернитесь на страницу **Владельцы** — имя будет удалено из списка владельцев.

## <a name="next-steps"></a>Дополнительная информация
- [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)

- [Настройка параметров групп с помощью командлетов Azure Active Directory](../users-groups-roles/groups-settings-cmdlets.md)

- [Использование групп для назначения доступа к интегрированному приложению SaaS](../users-groups-roles/groups-saasapps.md)

- [Интеграция локальных удостоверений с Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Настройка параметров групп с помощью командлетов Azure Active Directory](../users-groups-roles/groups-settings-v2-cmdlets.md)
