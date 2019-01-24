---
title: Добавление имеющейся подписки Azure в клиент Azure Active Directory | Документация Майкрософт
description: Инструкции по добавлению имеющейся подписки Azure в клиент Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 20c4be3741fa1e728cf59a148beb5adbc87b89c0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452035"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Связывание или добавление подписки Azure в клиент Azure Active Directory
Подписка Azure связана с Azure Active Directory отношением доверия. Это означает, что подписка доверяет Azure AD проверку подлинности пользователей, служб и устройств. Несколько подписок могут доверять одному и тому же каталогу Azure AD, но одна конкретная подписка будет доверять только одному каталогу.

Если срок действия подписки истекает, доступ к другим ресурсам, связанным с этой подпиской, также прекращается. Но каталог Azure AD остается в Azure, и вы можете управлять им, используя другую подписку Azure.

У всех ваших пользователей есть один "домашний" каталог для проверки подлинности. Но пользователи также могут быть гостями в других каталогах. Домашний и гостевые каталоги для каждого пользователя отображаются в Azure AD.

>[!Important]
>Все пользователи [управления доступом на основе ролей (RBAC)](../../role-based-access-control/role-assignments-portal.md) с назначенными правами доступа и все администраторы подписки потеряют доступ, если изменить каталог подписки. Кроме того, если у вас есть хранилища ключей, перемещение подписки повлияет и на них. Чтобы исправить это, необходимо [изменить идентификатора клиента хранилища ключей](../../key-vault/key-vault-subscription-move-fix.md) до операции возобновления.


## <a name="before-you-begin"></a>Перед началом работы
Прежде чем связать или добавить подписку, необходимо выполнить следующие действия:

- Войдите, используя учетную запись, которая:
    - Имеет доступ **владельца RBAC** к подписке.

    - Существует в текущем каталоге, связанном с подпиской, а также в новом каталоге, к которому вы хотите привязать подписку в дальнейшем. Дополнительные сведения о получении доступа к другому каталогу см. в статье [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](../b2b/add-users-administrator.md).

- Убедитесь, что не используете следующие подписки: поставщики облачных служб Azure (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), внутренняя подписка Майкрософт (MS-AZR-0015P) или Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Привязка имеющейся подписки к каталогу Azure AD
1. Войдите и выберите нужную подписку на [странице подписок на портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Выберите **Изменить каталог**.

    ![Страница подписок с выделенным параметром "Изменить каталог"](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Просмотрите все предупреждения и нажмите **Изменить**.

    ![Страница "Изменить каталог" с новым каталогом](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Каталог для подписки изменен, вы получаете сообщение об успешном выполнении.

    ![Сообщение об успешном выполнении](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Используйте переключатель каталогов, чтобы перейти в новый каталог. Отображение всех элементов может занять до 10 минут.

    ![Страница "Переключатель каталогов"](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Изменение каталога подписки происходит на уровне службы и не влияет на выставление счетов для подписки. Администратор учетной записи по-прежнему может изменить администратора службы в [Центре управления учетной записью](https://account.azure.com/subscriptions). Чтобы удалить исходный каталог, передайте права владения выставлением счетов по подписке новому администратору учетной записи. Сведения о передаче прав владения выставлением счетов см. в статье [Передача прав владения подпиской Azure другой учетной записи](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Дополнительная информация

- Сведения о создании нового клиента Azure AD см. в разделе [Получение доступа к Azure Active Directory для создания клиента](active-directory-access-create-new-tenant.md)

- Дополнительные сведения о том, как осуществляется доступ к ресурсам в Microsoft Azure, см. в статье [Основные сведения о доступе к ресурсам в Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Дополнительные сведения о том, как назначать роли в Azure AD, см. в разделе [Назначение ролей каталога для пользователей в Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
