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
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422897"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Назначение ролей администратора и других ролей пользователям с помощью Azure Active Directory
Если пользователю в вашей организации требуется разрешение на управление ресурсами Azure Active Directory (Azure AD), необходимо назначить пользователю соответствующую роль в Azure AD в зависимости от действий, которые пользователю нужно выполнять.

Дополнительные сведения о доступных ролях см. в разделе [назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Сведения о добавлении пользователей см. в статье [Добавление новых пользователей в Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Назначение ролей
Обычно в Azure AD роли назначаются пользователю на странице пользователя **Роль каталога**.

Вы также можете назначать роли с помощью управления привилегированными пользователями (PIM). Более подробные сведения о том, как использовать PIM, см. в разделе [Управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Назначение роли пользователю
1. Перейдите к [портал Azure](https://portal.azure.com/) и войдите в систему, используя учетную запись глобального администратора для каталога. 

2. Найдите и выберите **Azure Active Directory**.

      ![портал Azure Поиск Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Выберите **Пользователи**.

4. Найдите и выберите пользователя, который получает назначение роли. Например, _Alain Charon_.

      ![Страница "все пользователи" — Выбор пользователя](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. На странице **(Alain Чарон-Profile** выберите **назначенные роли**.

    Откроется страница **Ален Чарон — роль каталога**.

6. Выберите **добавить назначение**, выберите роль для назначения (Alain (например, _Администратор приложения_), а затем нажмите **кнопку Выбрать**.

    ![Страница "назначенные роли" — Отображение выбранной роли](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Алену Чарону назначена роль администратора приложения. Она отображается на странице **Ален Чарон — роль каталога**.

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей
Если необходимо удалить назначение роли для пользователя, это также можно сделать на странице **Ален Чарон — роль каталога**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Удаление назначения роли

1. Выберите **Azure Active Directory**, нажмите **Пользователи**, а затем найдите и выберите пользователя, у которого необходимо удалить роль. Например, _Alain Charon_.

2. Выберите **назначенные роли**, щелкните **Администратор приложения**, а затем выберите **удалить назначение**.

    ![Страница «назначенные роли», показывающая выбранную роль и параметр «Удалить»](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Назначение роли удалено, она больше не отображается на странице **Ален Чарон — роль каталога**.

## <a name="next-steps"></a>Дальнейшие действия
- [Добавление или удаление пользователей](add-users-azure-active-directory.md)

- [Добавление или изменение данных профиля](active-directory-users-profile-azure-portal.md)

- [Добавление гостевых пользователей из другого каталога](../b2b/what-is-b2b.md)

Кроме того, можно выполнять другие задачи по управлению пользователями, например назначение делегатов, использование политик и совместное использование учетных записей пользователей. Дополнительные сведения о других доступных действиях см. в [документации по управлению пользователями Azure Active Directory](../users-groups-roles/index.yml).


