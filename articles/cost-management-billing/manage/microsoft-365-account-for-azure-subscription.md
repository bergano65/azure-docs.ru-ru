---
title: Регистрация в Azure с помощью учетной записи Microsoft 365
description: Узнайте, как создать подписку Azure, используя учетную запись Microsoft 365. Вы также можете связать существующие учетные записи Azure и Microsoft 365.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: eb266680cc514b1e2d70e510ed65e9a7790c0260
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370340"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Регистрация для получения подписки Azure с помощью учетной записи Microsoft 365

Если у вас есть подписка Microsoft 365, для создания подписки Azure можно использовать учетную запись Microsoft 365. Войдите на [портал Azure](https://portal.azure.com/), используя имя пользователя и пароль Microsoft 365. Если вы хотите настроить виртуальные машины или использовать другие службы Azure, то необходимо зарегистрироваться для получения подписки Azure. Вы можете предоставить другим пользователям доступ к своей подписке Azure, а также [использовать управление доступом на основе ролей (Azure RBAC) для управления доступом к подписке и ресурсам Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Если у вас уже есть учетная запись Microsoft 365 и подписка Azure, см. статью [Связывание клиента Microsoft 365 с подпиской Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Получение подписки Azure с помощью учетной записи Microsoft 365

Зарегистрировавшись в Azure с использованием имени пользователя и пароля Microsoft 365, вы сэкономьте время и избежите ситуации с увеличением числа учетных записей.

1. Зарегистрируйтесь на сайте [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Войдите с использованием имени пользователя и пароля Microsoft 365. Используемой учетной записи не нужны права администратора. Если у вас есть несколько учетных записей Microsoft 365, убедитесь, что используются учетные данные учетной записи Microsoft 365, которую вы хотите связать с подпиской Azure.

   ![Снимок экрана, на котором показана страница входа.](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Введите необходимые сведения и завершите процесс регистрации. Некоторые данные можно не указывать, если у вас уже есть учетная запись Microsoft 365.

    ![Снимок экрана, на котором показана форма регистрации.](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Если необходимо добавить других пользователей организации в подписку Azure, то см. статью [Начало работы с управлением доступом на портале Azure](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Дополнительные сведения о подписках Azure и Microsoft 365</a>

Microsoft 365 и Azure используют службу Azure AD для управления пользователями и подписками. Считайте, что каталог Azure — это контейнер, в котором можно группировать пользователей и подписки. Чтобы использовать одну учетную запись пользователя для подписок Azure и Microsoft 365, подписки Azure должны быть созданы в одном каталоге с подписками Microsoft 365. Помните на следующие моменты.

* Подписка создается в каталоге.
* Пользователи принадлежат к каталогам.
* Подписка сохраняется в каталоге пользователя, который ее создал. Подписка Microsoft 365 и Azure привязаны к одной учетной записи.
* Подписки Azure принадлежат отдельным пользователям в каталоге.
* Подписки Microsoft 365 принадлежат самому каталогу. Пользователи с соответствующими разрешениями в каталоге могут управлять этими подписками.

![Снимок экрана, на котором показана связь между каталогом, пользователями и подписками.](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Дополнительные сведения см. в статье [Связь между подписками Azure и службой Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия

- Предоставьте другим пользователям доступ к своей подписке Azure и [используйте управление доступом на основе ролей (Azure RBAC) для управления доступом к подписке и ресурсам Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).