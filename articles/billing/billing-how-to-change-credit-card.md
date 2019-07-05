---
title: Изменение кредитной карты для Azure
description: В этой статье описывается изменение кредитной карты, используемой для оплаты подписки Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7c04e33d6199d3930be28ce84458e9c3a743eb8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491345"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Добавление, обновление или удаление кредитной карты для Azure

На портале Azure можно добавить новую кредитную карту, обновление существующей кредитной карты или удалить кредитную карту, которые не используются. Чтобы внести изменения, необходимо быть [администратором учетной записи](billing-subscription-transfer.md#whoisaa).

Если у вас есть [соглашении клиента Microsoft](#check-access-to-a-microsoft-customer-agreement), способами оплаты связаны с выставления счетов профилей. Узнайте, как [изменить метод оплаты по умолчанию для профиля выставления счетов](#change-payment-method-for-a-billing-profile). Только пользователь, зарегистрировались в Azure можно обновить метод оплаты.

**Хотите переключиться на оплату по счету (проверка/банковский перевод)?** см. в статье [Отправка запроса на оплату подписки Azure по счету](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Добавление новой кредитной карты для подписки Azure

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Поиск **Cost Management + выставление счетов**.

    ![Снимок экрана, показывающий поиска](./media/billing-how-to-change-credit-card/search.png)

1. Выберите подписку, вы бы хотели добавить кредитную карту для.
1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. В левом верхнем углу щелкните значок "+", чтобы добавить карту. Справа отобразится форма для добавления кредитной карты.
1. Введите сведения о кредитной карте.

    ![Снимок экрана, демонстрирующий Добавление новой карточки.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Чтобы облегчить эту карточку активного метода оплаты, установите флажок рядом с полем **сделать этот метод оплаты активным** над формой. После этого такая карта станет активным платежным средством для всех подписок, в которых используется та же карта, что и в выбранной подписке.

1. Щелкните **Далее**.

Если после добавления кредитной карты появится ошибка, см. статью [Банковская или кредитная карта отклонена при регистрации в Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Обновление существующей кредитной карты

Если обновлена вашей кредитной карты, а номер остается прежним, обновите существующие сведения о кредитной карте, такие как дату истечения срока действия. Если номер кредитной карты изменен из-за потери, кражи или истечения срока ее действия, следуйте указаниям в разделе [Добавление кредитной карты в качестве метода оплаты](#addcard). Не нужно обновлять код безопасности карты (CVV).

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Поиск **Cost Management + выставление счетов**.

    ![Снимок экрана, показывающий поиска](./media/billing-how-to-change-credit-card/search.png)

1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Щелкните кредитной карты, который вы хотите изменить. Справа отобразится форма для добавления кредитной карты.

    ![Снимок экрана с кредитной карты, выбранной.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Обновите сведения о кредитной карте.
1. Щелкните **Сохранить**.

## <a name="use-a-different-credit-card"></a>Использовать другую кредитную карту

Если более чем одной из ваших подписок имеют один и тот же метод оплаты активным, измените метод оплаты активным на любую из этих подписок также обновляет метод оплаты активным на другие.

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Поиск **Cost Management + выставление счетов**.

    ![Снимок экрана, показывающий поиска](./media/billing-how-to-change-credit-card/search.png)

1. Выберите подписку, вы бы хотели добавить кредитную карту для.
1. Выберите **Методы оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Установите флажок рядом с картой, вы бы хотели сделать метод оплаты активным.
1. Нажмите кнопку **активизировать**.
    ![Снимок экрана, показывающий кредитной карты установлен и имеет значение active.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Удаление кредитной карты из учетной записи

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Выберите **управление затратами + выставление счетов** в левой части страницы.

    ![Снимок экрана, показывающий поиска](./media/billing-how-to-change-credit-card/search.png)

1. В разделе **выставления счетов**выберите **способами оплаты**.

    ![Снимок экрана, отображающий выбранный параметр "Управление методами оплаты".](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Установите флажок рядом с картой, который требуется удалить.
1. Нажмите кнопку **Delete**(Удалить).

Если активный метод оплаты для любой из подписок Microsoft вашей кредитной карты, его нельзя удалить из учетной записи Azure. Измените метод оплаты активным для всех подписок, связанных с этой кредитной карты и повторите попытку
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Изменить метод оплаты для профиля выставления счетов

Чтобы изменить способ оплаты для профиля выставления счетов, необходимо быть тому, кто зарегистрировались в Azure.

Если вы хотите переключить метод оплаты по умолчанию для проверки подключения передачи, узнайте, как [переключения профиля выставления счетов для проверки подключения передачи](billing-how-to-pay-by-invoice.md).

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. В меню слева щелкните **профили счетов**.

    ![Снимок экрана, показывающий профиль выставления счетов в меню](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Выберите профиль выставления счетов.
1. В меню слева, выберите **способами оплаты**.

   ![Снимок экрана, показывающий способы оплаты в меню](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Над способ оплаты по умолчанию, нажмите кнопку **изменение**.

    ![Снимок экрана: кнопка "Изменить"](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Выберите существующую карту или добавить новую.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
Ниже разделах даны ответы на часто задаваемые вопросы об изменении данных кредитной или дебетовой карты.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Моя подписка отключена. Почему не удается удалить кредитную карту?

После того как подписка была отключена или отменена, она будет удалена только через 90 дней. На случай, если вы захотите снова активировать подписку, мы сохраняем ваш метод оплаты в файле на протяжении периода удержания. После этого подписки будут окончательно удалены.

Если необходимо удалить вашей кредитной карты, до окончания 90-дневного срока хранения, [повторно активировать подписку](billing-subscription-become-disable.md). Если вам не удастся это сделать, [обратитесь в службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Почему отображается ошибка "Срок действия сеанса истек. Щелкните здесь, чтобы войти обратно"?

Если это сообщение об ошибке появляется даже после выполнения повторного входа, повторите попытку в режиме закрытого просмотра.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Как использовать разные карты для всех имеющихся подписок?

К сожалению, если для подписок уже используется одна карта, использование для них разных карт невозможно. Тем не менее, при регистрации новой подписки для нее можно использовать новый метод оплаты.

### <a name="how-do-i-make-payments"></a>Как производить оплату?

Если вы настроили кредитной карты в качестве метода оплаты, то оплата производится автоматически с вашей карты после каждого расчетного периода. Вам не нужно ничего делать.

Если вы используете [оплату по счету](billing-how-to-pay-by-invoice.md), отправьте платеж в местоположение, указанное внизу счета.

### <a name="how-do-i-change-the-tax-id"></a>Как изменить код налогоплательщика?

Чтобы добавить или обновить код налогоплательщика, обновите свой профиль в [центре управления учетной записью](https://account.azure.com/Profile), а затем выберите **Налоговая**. Этот код налогоплательщика используется для расчета налоговых льгот и отображается в счете.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверьте доступ к соглашению клиента Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или нужна помощь, [создать запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [Azure резервирования](billing-save-compute-costs-reservations.md) для просмотра, если они могут сэкономить ваши деньги.
