---
title: Регистрация подписки Microsoft 365 с помощью учетной записи Azure
description: Узнайте, как создать подписку Microsoft 365 с помощью учетной записи Azure
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: banders
ms.openlocfilehash: 006f8edc81ec19647ff6173a4c786c6d7e93fadc
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810386"
---
# <a name="sign-up-for-a-microsoft--365-subscription-with-your-azure-account"></a>Регистрация подписки Microsoft 365 с помощью учетной записи Azure

Если вы являетесь подписчиком Azure, вы можете использовать учетную запись Azure для регистрации подписки Microsoft 365. Если вы работаете в организации, которая имеет подписку Azure, вы можете создать подписку Microsoft 365 для пользователей в существующей службе Azure Active Directory (Azure AD). Зарегистрируйтесь для использования Microsoft 365, используя учетную запись с разрешениями глобального администратора или администратора выставления счетов в арендаторе Azure Active Directory. Дополнительные сведения см. в разделе [Проверка разрешений учетной записи в Azure AD](#RoleInAzureAD) и в статье [Назначение ролей администратора в Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Если у вас уже есть учетная запись Microsoft 365 и подписка Azure, вы можете [связать клиент Microsoft 365 с подпиской Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Получение подписки Microsoft 365 с помощью учетной записи Azure

1. Перейдите на [страницу продукта Microsoft 365](https://www.microsoft.com/microsoft-365/business/all-business) и выберите план.
2. Щелкните **Войти** в правом верхнем углу страницы.

    ![Снимок экрана: страница пробной версии Microsoft 365](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Войдите, используя свои учетные данные Azure. При создании подписки для организации используйте учетную запись Azure, которая является участником роли каталога глобального администратора или администратора выставления счетов каталога в клиенте Azure Active Directory.

    ![Снимок экрана: вход в учетную запись Майкрософт](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Щелкните **Попробовать**.

    ![Снимок экрана: уведомление о подтверждении заказа Microsoft 365](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. На странице подтверждения заказа щелкните **Продолжить**.

    ![Снимок экрана: сообщение о получении заказа на Microsoft 365](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Теперь все готово. Создавая подписку Microsoft 365 для организации, проверьте, связаны ли пользователи Azure AD с Microsoft 365, выполнив следующие действия.

1. Откройте Центр администрирования Microsoft 365.
2. Разверните раздел **Пользователи** и выберите **Активные пользователи**.

    ![Снимок экрана с пользователями в Центре администрирования Microsoft 365](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

После регистрации подписка Microsoft 365 будет добавлена в тот же экземпляр Azure Active Directory, в который входит ваша подписка Azure. Дополнительные сведения см. в разделе [Справочная информация о подписках Azure и Microsoft 365](microsoft-365-account-for-azure-subscription.md#more-about-subs) и статье [Связь между подписками Azure и службой Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Проверка разрешений учетной записи в Azure AD
1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Все службы** и найдите **Active Directory**.

    ![Снимок экрана с Active Directory на портале Azure](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Щелкните **Пользователи и группы** > **Все пользователи**.
4. Выберите пользователя.

    ![Снимок экрана, на котором показаны пользователи Azure Active Directory](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Щелкните **Роль каталога**.

    ![Снимок экрана, на котором показана роль каталога на портале Azure](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  Роли **Глобальный администратор** или **Администратор с ограниченными правами** > **Администратор выставления счетов** требуются, чтобы создать подписку Microsoft 365 для пользователей в существующем экземпляре Azure Active Directory.

    ![Снимок экрана, на котором показана роль каталога "Администратор выставления счетов" на портале Azure](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия

- [Связывание клиента Microsoft 365 с подпиской Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
