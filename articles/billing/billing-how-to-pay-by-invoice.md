---
title: Оплата подписок Azure по счету-фактуре
description: Описывает способ оплаты для подписок Azure по счету.
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
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491222"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Оплата счета для подписки Azure

Если переключиться на оплату по счету, это означает, что оплачивать счета в течение 30 дней с даты счета путем проверки/банковский перевод. Чтобы получить право для оплаты счета-фактуры для вашей подписки Azure, отправьте запрос в службу поддержки Azure. После утверждения запроса вы можете переключать на оплату (проверка/банковский перевод) в [портала Azure](https://portal.azure.com).

> [!IMPORTANT]
> * Оплата по счету (проверка/банковский перевод) доступна только для учетных записей организаций.
> * Перед переключением на оплату следует обращать все непогашенные задолженности.

## <a name="request-to-pay-by-invoice"></a>Запрос на оплату по счету-фактуре

1. Войдите на [портале Azure](https://portal.azure.com/). Выберите **Справка и поддержка** > **Новый запрос на получение поддержки**.

    ![Справка и поддержка ссылку](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Выберите **выставления счетов** как **тип проблемы**. *Тип проблемы* — это категория запросов поддержки. Выберите подписку, для которой требуется оплата по счету, выберите план поддержки и затем выберите **Далее**.

3. Выберите в поле **Тип проблемы** пункт **Оплата счета**. *Тип проблемы* является подкатегории запроса поддержки.

4. Укажите следующие сведения в поле **Сведения** и нажмите кнопку **Далее**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - **Название компании** и **компании адрес** должны соответствовать сведениям, указанный для учетной записи Azure. Чтобы просмотреть или обновить данные, см. в разделе [изменение сведений в профиле учетной записи Azure](billing-how-to-change-azure-account-profile.md).
    - Прежде, чем может быть утвержден кредитный лимит, необходимо добавить контактные данные выставления счетов на портале Azure. Сведения о контакте должен быть связан с Зарплата или финансового отдела компании. Чтобы обновить контактные данные выставления счетов, перейдите в [центре управления учетной записью](https://account.azure.com/Profile).

5. Проверьте свои контактные данные и предпочтительный способ связи и нажмите кнопку **Создать**.

Если нужно выполнить проверку кредитоспособности связи с большим объемом кредита, мы отправим вам соответствующее заявление выполнить проверку.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Переключиться на оплату (проверка/банковский перевод)

После утверждения на оплату по счету вы можете на оплату (проверка/банковский перевод) на портале Azure.

При наличии учетной записи программы Microsoft Online Services, вы можете подписки Azure для передачи проверки подключения. Если у вас есть соглашение клиента Microsoft, вы можете профиль выставления счетов для передачи проверки подключения. [Узнайте, как проверить тип учетной записи](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Переключить подписку Azure для передачи проверки подключения

Выполните следующие действия, чтобы переключить свою подписку Azure на оплату (проверка/банковский перевод). **После перехода на оплату (проверка/банковский перевод) Невозможно переключиться обратно кредитной карты**.

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Поиск **Cost Management + выставление счетов**.

    ![Снимок экрана, показывающий поиска](./media/billing-how-to-pay-by-invoice/search.png)

1. Выберите подписку, которую вы хотите переключиться на счет оплаты.
1. Выберите **Методы оплаты**.
1. На панели команд нажмите кнопку **оплаты счета-фактуры** кнопки.

    ![Снимок экрана, показывающий Оплата с помощью кнопки](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Переключение профиль выставления счетов для проверки подключения передачи

Выполните следующие шаги для переключения профиля выставления счетов для передачи проверки подключения. Обратите внимание на то, что только пользователь, выполнивший зарегистрировались в Azure можно изменить способ оплаты по умолчанию для профиля выставления счетов.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. В меню слева щелкните **профили счетов**.

    ![Снимок экрана, показывающий профиль выставления счетов в меню](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Выберите профиль выставления счетов.
1. В меню слева, выберите **способами оплаты**.

   ![Снимок экрана, показывающий способы оплаты в меню](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Щелкните синий баннер, говорящее, что вы имеете право на оплату по проверка/банковский перевод.

    ![Снимок экрана, показывающий синий баннер, чтобы переключиться в режим проверки подключения](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверьте доступ к соглашению клиента Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или нужна помощь, [создать запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия

- При необходимости обновите контактные данные выставления счетов в [центре управления учетной записью](https://account.azure.com/Profile).
