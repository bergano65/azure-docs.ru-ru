---
title: Назначение ролей каталога пользователям в Azure Active Directory | Документация Майкрософт
description: Инструкции по назначению ролей администратора и других ролей пользователям с помощью Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2247b7678573594c4929bd33747fffb91203f457
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561797"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Назначение ролей администратора и других ролей пользователям с помощью Azure Active Directory
Если пользователю в вашей организации требуется разрешение на управление ресурсами Azure Active Directory (Azure AD), необходимо назначить пользователю соответствующую роль в Azure AD в зависимости от действий, которые пользователю нужно выполнять.

Дополнительные сведения о доступных ролях см. в статье [Назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Сведения о добавлении пользователей см. в статье [Добавление новых пользователей в Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Назначение ролей
Обычно в Azure AD роли назначаются пользователю на странице пользователя **Роль каталога**.

Вы также можете назначать роли с помощью управления привилегированными пользователями (PIM). Более подробные сведения о том, как использовать PIM, см. в разделе [Управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Назначение роли пользователю
1. Войдите на [портал Azure](https://portal.azure.com/) с учетной записью глобального администратора каталога.

2. Выберите **Azure Active Directory**, нажмите **Пользователи**, а затем найдите и выберите пользователя, которому необходимо назначить роль. Например, _Alain Charon_.

3. На странице **Ален Чарон — профиль** выберите **Роль каталога**.

    Откроется страница **Ален Чарон — роль каталога**.

4. Выберите **Добавить роль**, выберите роль для Алена (например, _Администратор приложения_), а затем нажмите **Выбрать**.

    ![Страница роли каталога с выбранной ролью](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Алену Чарону назначена роль администратора приложения. Она отображается на странице **Ален Чарон — роль каталога**.

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей
Если необходимо удалить назначение роли для пользователя, это также можно сделать на странице **Ален Чарон — роль каталога**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Удаление назначения роли

1. Выберите **Azure Active Directory**, нажмите **Пользователи**, а затем найдите и выберите пользователя, у которого необходимо удалить роль. Например, _Alain Charon_.

2. Выберите **Роль каталога**, **Администратор приложения**, а затем нажмите **Удалить роль**.

    ![Страница роли каталога с выбранной ролью и параметром удаления](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Назначение роли удалено, она больше не отображается на странице **Ален Чарон — роль каталога**.

## <a name="next-steps"></a>Следующие шаги
- [Добавление или удаление пользователей](add-users-azure-active-directory.md)

- [Добавление или изменение данных профиля](active-directory-users-profile-azure-portal.md)

- [Добавление гостевых пользователей из другого каталога](../b2b/what-is-b2b.md)

Кроме того, можно выполнять другие задачи по управлению пользователями, например назначение делегатов, использование политик и совместное использование учетных записей пользователей. Дополнительные сведения о других доступных действиях см. в [документации по управлению пользователями Azure Active Directory](../users-groups-roles/index.yml).


