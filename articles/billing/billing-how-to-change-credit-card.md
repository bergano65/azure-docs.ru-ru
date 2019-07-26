---
title: Изменение кредитной карты для Azure
description: Описывает, как изменить кредитную карту, используемую для оплаты подписки Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383659"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Добавление, обновление или удаление кредитной карты для Azure

В портал Azure можно добавить новую кредитную карту, обновить существующую кредитную карту или удалить неиспользуемую кредитную карту. Чтобы внести изменения, необходимо быть [администратором учетной записи](billing-subscription-transfer.md#whoisaa).

Если у вас есть [соглашение с клиентами Майкрософт](#check-access-to-a-microsoft-customer-agreement), способы оплаты связаны с профилями выставления счетов. Узнайте, как [изменить метод оплаты по умолчанию для профиля выставления счетов](#change-payment-method-for-a-billing-profile). Только пользователь, выполнивший вход в Azure, может обновить метод оплаты.

**Хотите переключиться на оплату по счету (проверка/проводка передачи)?** см. в статье [Отправка запроса на оплату подписки Azure по счету](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Добавление новой кредитной карты в подписку Azure

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Выполните поиск по запросу **Управление затратами и выставление счетов**.

    ![Снимок экрана, показывающий Поиск](./media/billing-how-to-change-credit-card/search.png)

1. Выберите подписку, в которую хотите добавить кредитную карту.
1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. В левом верхнем углу щелкните значок "+", чтобы добавить карту. Справа отобразится форма для добавления кредитной карты.
1. Введите сведения о кредитной карте.

    ![Снимок экрана, показывающий Добавление новой карточки.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Чтобы сделать эту карточку активным методом оплаты, установите флажок **сделать этот активный метод оплаты** над формой. После этого такая карта станет активным платежным средством для всех подписок, в которых используется та же карта, что и в выбранной подписке.

1. Щелкните **Далее**.

Если после добавления кредитной карты появится ошибка, см. статью [Банковская или кредитная карта отклонена при регистрации в Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Обновление существующей кредитной карты

Если ваша кредитная карта продлена и номер остается прежним, обновите существующие сведения о кредитной карте, такие как Дата окончания срока действия. Если номер кредитной карты изменен из-за потери, кражи или истечения срока ее действия, следуйте указаниям в разделе [Добавление кредитной карты в качестве метода оплаты](#addcard). Не нужно обновлять код безопасности карты (CVV).

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Выполните поиск по запросу **Управление затратами и выставление счетов**.

    ![Снимок экрана, показывающий Поиск](./media/billing-how-to-change-credit-card/search.png)

1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Щелкните кредитную карту, которую вы хотите изменить. Справа отобразится форма для добавления кредитной карты.

    ![Снимок экрана, на котором показана выбранная кредитная карта.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Обновите сведения о кредитной карте.
1. Щелкните **Сохранить**.

## <a name="use-a-different-credit-card"></a>Использовать другую кредитную карту

Если в нескольких подписках есть один и тот же активный метод оплаты, то изменение активного метода оплаты для любой из этих подписок также обновляет активный метод оплаты для остальных.

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Выполните поиск по запросу **Управление затратами и выставление счетов**.

    ![Снимок экрана, показывающий Поиск](./media/billing-how-to-change-credit-card/search.png)

1. Выберите подписку, в которую хотите добавить кредитную карту.
1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Установите флажок рядом с картой, которую вы хотите сделать активным методом оплаты.
1. Щелкните **Задать активный**.
    ![Снимок экрана, на котором показана выбранная кредитная карта и задана активная.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Удаление кредитной карты из учетной записи

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. В левой части страницы выберите **Управление затратами и выставление счетов** .

    ![Снимок экрана, показывающий Поиск](./media/billing-how-to-change-credit-card/search.png)

1. В разделе **выставление счетов**выберите **методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Установите флажок рядом с картой, которую необходимо удалить.
1. Нажмите кнопку **Delete**(Удалить).

Если ваша кредитная карта является активным методом оплаты для любой подписки Майкрософт, вы не сможете удалить ее из учетной записи Azure. Измените активный метод оплаты для всех подписок, связанных с этой кредитной картой, и повторите попытку.
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Изменение способа оплаты для профиля выставления счетов

Чтобы изменить способ оплаты для профиля выставления счетов, необходимо быть пользователем, который подписался на Azure.

Если вы хотите переключить метод оплаты по умолчанию на проверку или перенос проводки, Узнайте, как переключить [профиль выставления счетов на проверку или перенос проводки](billing-how-to-pay-by-invoice.md).

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. В меню слева щелкните **Профили выставления счетов**.

    ![снимок экрана, показывающий профиль выставления счетов в меню](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Выберите профиль выставления счетов.
1. В меню слева выберите **методы оплаты**.

   ![Снимок экрана, показывающий способы оплаты в меню](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Выше метода оплаты по умолчанию нажмите кнопку **изменить**.

    ![Снимок экрана, на котором отображается кнопка "Изменить"](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Выберите существующую карточку или добавьте новую.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
В следующих разделах вы получите ответы на часто задаваемые вопросы об изменении данных кредитной карты.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Моя подписка отключена. Почему не удается удалить кредитную карту?

После того как подписка была отключена или отменена, она будет удалена только через 90 дней. На случай, если вы захотите снова активировать подписку, мы сохраняем ваш метод оплаты в файле на протяжении периода удержания. После этого подписка будет окончательно удалена.

Если вам нужно удалить кредитную карту до окончания срока хранения 90 дней, [повторно активируйте подписку](billing-subscription-become-disable.md). Если вам не удастся это сделать, [обратитесь в службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Почему отображается ошибка "Срок действия сеанса истек. Щелкните здесь, чтобы войти обратно"?

Если это сообщение об ошибке появляется даже после выполнения повторного входа, повторите попытку в режиме закрытого просмотра.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Как использовать разные карты для всех имеющихся подписок?

К сожалению, если для подписок уже используется одна карта, использование для них разных карт невозможно. Тем не менее, при регистрации новой подписки для нее можно использовать новый метод оплаты.

### <a name="how-do-i-make-payments"></a>Как производить оплату?

Если в качестве метода оплаты настроена кредитная карта, плата будет автоматически взиматься после каждого расчетного периода. Вам не нужно ничего делать.

Если вы используете [оплату по счету](billing-how-to-pay-by-invoice.md), отправьте платеж в местоположение, указанное внизу счета.

### <a name="how-do-i-change-the-tax-id"></a>Как изменить код налогоплательщика?

Чтобы добавить или обновить идентификатор налога, обновите профиль в [центр управления учетной записью Azure](https://account.azure.com/Profile), а затем выберите **Налоговая запись**. Этот код налогоплательщика используется для расчета налоговых льгот и отображается в счете.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к соглашению клиента Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Нужна помощь? Свяжитесь с нами.

Если у вас возникли вопросы или вам нужна помощь, [Создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Следующие шаги
- Узнайте о [резервировании Azure](billing-save-compute-costs-reservations.md) , чтобы узнать, могут ли они сэкономить деньги.
