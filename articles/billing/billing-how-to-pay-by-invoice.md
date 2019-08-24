---
title: Оплата за подписки Azure по счету
description: В этой статье описывается, как платить за подписки Azure по счету.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012600"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Оплата подписки Azure по счету

Если переключиться на оплату по счету, это означает, что счет будет оплачиваться в течение 30 дней с даты выставления счета на проверку/проводное перемещение. Чтобы получить возможность платить за подписку Azure по счету, отправьте запрос в службу поддержки Azure. После утверждения запроса можно переключиться на оплату счета (проверка/проводка передачи) в [портал Azure](https://portal.azure.com).

> [!IMPORTANT]
> * Оплата по счету (проверка/проводка передачи) доступна только для бизнес-учетных записей.
> * Выплатить все неоплаченные расходы, прежде чем переходить к оплате по счету.
> * В настоящее время оплата счетов не поддерживается для глобального Azure в Китае.

## <a name="request-to-pay-by-invoice"></a>Запрос на оплату по счету

1. Войдите на [портале Azure](https://portal.azure.com/). Выберите **Справка и поддержка** > **Новый запрос на получение поддержки**.

    ![Ссылка на справку и поддержку](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. В качестве **типа проблемы**выберите **выставление счетов** . *Тип проблемы* — Категория запросов на поддержку. Выберите подписку, для которой необходимо оплатить счет, выберите план поддержки, а затем нажмите кнопку **Далее**.

3. Выберите **Оплата** в качестве **типа проблемы**. *Тип проблемы* — Подкатегория запроса поддержки.

4. Выберите **Переключить на оплату по счету** в качестве подтипа **проблемы** .

5. Укажите следующие сведения в поле **Сведения** и нажмите кнопку **Далее**.

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

    - **Название компании** и **адрес компании** должны соответствовать сведениям, указанным для учетной записи Azure. Чтобы просмотреть или обновить сведения, см. статью [изменение данных профиля учетной записи Azure](billing-how-to-change-azure-account-profile.md).
    - Добавьте контактные данные для выставления счетов в портал Azure до утверждения кредитного лимита. Контактные данные должны быть связаны с кредиторской задолженностью компании или финансовым отделом. Чтобы обновить контактные данные для выставления счетов, перейдите по адресу [центр управления учетной записью Azure](https://account.azure.com/Profile).

6. Проверьте свои контактные данные и предпочтительный способ связи и нажмите кнопку **Создать**.

Если необходимо выполнить проверку кредита из-за необходимого количества кредита, мы отправим вам платежное приложение проверки кредита.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Переключиться на оплату по счету (проверка/перенос проводки)

После утверждения оплаты по счету можно переключиться на оплату счета (проверка/проводка передачи) в портал Azure.

Если у вас есть учетная запись в рамках программы Microsoft Online Services, вы можете переключить подписку Azure на оплату чеком или банковским переводом. С помощью соглашения клиента Майкрософт вы можете переключить профиль выставления счетов на проверку или перенос проводки. [Узнайте, как проверить тип учетной записи](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Переключение подписки Azure на проверку и перенос проводок

Выполните следующие действия, чтобы переключить подписку Azure на счет оплаты (проверка/проводка передачи). **После переключения на оплату счета (проверка/передача проводки) вы не сможете вернуться на кредитную карту**.

1. Войдите на [портал Azure](https://portal.azure.com) в качестве администратора учетной записи.
1. Выполните поиск по запросу **Управление затратами и выставление счетов**.

    ![Снимок экрана, показывающий Поиск](./media/billing-how-to-pay-by-invoice/search.png)

1. Выберите подписку, для которой вы хотели бы перейти на оплату по счету.
1. Выберите **Методы оплаты**.
1. На панели команд нажмите кнопку **Оплата по счету** .

    ![Снимок экрана, на котором отображается кнопка "Оплата по счету"](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Переключение профиля выставления счетов на проверку и перенос проводок

Выполните следующие действия, чтобы переключить профиль выставления счетов на проверку или перенос проводки. Только пользователь, выполнивший вход в Azure, может изменить метод оплаты по умолчанию для профиля выставления счетов.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. В меню слева щелкните **Профили выставления счетов**.

    ![снимок экрана, показывающий профиль выставления счетов в меню](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Выберите профиль выставления счетов.
1. В меню слева выберите **методы оплаты**.

   ![Снимок экрана, показывающий способы оплаты в меню](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Щелкните синий баннер с сообщением о том, что вы имеете право на оплату проверки/передачи.

    ![Снимок экрана, на котором показан синий баннер для переключения на проверку/проводку](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к соглашению клиента Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас возникли вопросы или вам нужна помощь, [Создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Следующие шаги

- При необходимости обновите контактные данные для выставления счетов на [центр управления учетной записью Azure](https://account.azure.com/Profile).
