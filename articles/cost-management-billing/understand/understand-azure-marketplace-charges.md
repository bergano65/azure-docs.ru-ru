---
title: Основная информация о расходах на внешние службы в Azure | Документация Майкрософт
description: Узнайте больше о выставлении счетов за использование внешних служб, ранее известных как Marketplace, в Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 120f60698851bcdaf39f989b4d36c0436b716833
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117871"
---
# <a name="understand-your-azure-external-services-charges"></a>Основная информация о расходах на внешние службы в Azure
Внешние службы публикуются сторонними поставщиками программного обеспечения в Azure Marketplace. Например, SendGrid — это внешняя служба, которую можно приобрести в Azure, но которая не опубликована корпорацией Майкрософт. Некоторые продукты Майкрософт также продаются через Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Как выставляются счета за внешние службы

- Если у вас есть Клиентское соглашение Майкрософт (MCA) или Соглашение с партнером Майкрософт (MPA), то ваши службы сторонних поставщиков будут оплачиваться вместе с остальными службами Azure. [Проверьте тип учетной записи выставления счетов](#check-billing-account-type), чтобы понять, есть ли у вас доступ к MCA или MPA.
- Если у вас нет MCA или MPA, счета за внешние службы оплачиваются отдельно от служб Azure. Вы получите два счета за каждый расчетный период: один счет для служб Azure, а второй — для покупок в Marketplace.
- У каждой внешней службы своя модель выставления счетов. Некоторые службы оплачиваются по мере использования, в то время как для других установлена фиксированная ежемесячная оплата.
- Для оплаты внешних служб невозможно использовать бесплатные кредитные средства. Если вы используете подписку Azure, которая включает в себя [бесплатные деньги на счете](https://azure.microsoft.com/pricing/spending-limits/), то ими невозможно оплачивать счета за внешние службы. При подготовке новой внешней службы или ресурса отображается предупреждение.

    ![Предупреждение о покупке Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Внешние расходы для клиентов с соглашением Enterprise

Клиенты с соглашением Enterprise могут просматривать данные о расходах на внешние службы и скачивать отчеты на портале EA. Чтобы приступить к работе, см. статью [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace для клиентов с соглашением Enterprise).

## <a name="view-and-download-invoices-for-external-services"></a>Просмотр и скачивание счетов для внешних служб

Если у вас есть Клиентское соглашение Майкрософт (MCA) или Соглашение с партнером Майкрософт (MPA), то ваши службы сторонних поставщиков будут оплачиваться вместе с остальными службами Azure по одному счету. [Проверьте тип учетной записи выставления счетов](#check-billing-account-type), чтобы понять, есть ли у вас доступ к MCA или MPA. Ознакомьтесь, как [просмотреть и скачать счета на портале Azure](download-azure-invoice.md), чтобы узнать о ваших расходах на сторонних поставщиков.

Если у вас нет MCA или MPA, вы получите отдельный счет на оплату услуг сторонних поставщиков. 

Расходы на Azure Marketplace отображаются в местной валюте.

Вы можете просмотреть и скачать счета для Azure Marketplace из портала Azure, выполнив следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выполните поиск по фразе **Управление затратами + выставление счетов**.
1. В меню слева выберите **Счета**.
1. В раскрывающемся списке подписок выберите подписку, связанную с вашими службами Marketplace.
1. Проверьте столбец **Тип** в списке счетов. Если счет предназначен для службы Marketplace, этот тип будет иметь значение **Azure Marketplace и резервирования**. 

    ![Снимок экрана: тип Azure Marketplace в сетке счетов](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Чтобы выполнить фильтрацию по типу для поиска только счетов для Azure Marketplace и резервирований, выберите фильтр **Тип**. Затем выберите **Azure Marketplace и резервирования** в раскрывающемся списке.

    ![Снимок экрана с выбранными фильтром "Тип" и элементом "Azure Marketplace и резервирование" в раскрывающемся списке](./media/understand-azure-marketplace-charges/type-filter.png)

1. Щелкните значок скачивания справа от счета, который необходимо скачать.

    ![Снимок экрана, на котором показан значок скачивания счета](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. В разделе **Счет** выберите синюю кнопку **Скачать**.

    ![Снимок экрана, на котором показана кнопка для скачивания счета в области контекста](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Оплата внешних служб на портале Azure

Если у вас есть Клиентское соглашение Майкрософт (MCA) или Соглашение с партнером Майкрософт (MPA), то ваши службы сторонних поставщиков будут оплачиваться вместе с остальными службами Azure. [Проверьте тип учетной записи выставления счетов](#check-billing-account-type), чтобы понять, есть ли у вас доступ к MCA или MPA. Если вы сделаете это, вы можете заплатить за весь счет на портале Azure, выполнив действия, описанные в статье [Как оплатить счет за Microsoft Azure](pay-bill.md).

Если у вас нет MCA или MPA, вы можете платить за счета Marketplace на портале Azure, выполнив следующие действия:

1. Выполните действия, описанные в предыдущем разделе [Просмотр и скачивание счетов для внешних служб](#view-and-download-invoices-for-external-services), чтобы найти счета Marketplace.
1. Щелкните синюю ссылку **Оплатить сейчас** на счет, который нужно оплатить.

    ![Снимок экрана, на котором показана ссылка "Оплатить сейчас", выбранная в сетке счетов](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Вы увидите ссылку **Оплатить сейчас** только в том случае, если тип счета — **Azure Marketplace и резервирования**, а состояние оплаты счета — "Срок проверки" или "Просрочено".

1. На новой странице щелкните синюю ссылку **Выберите метод оплаты**.

    ![Снимок экрана, на котором показана выбранная ссылка "Выберите метод оплаты"](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Выбрав метод оплаты, нажмите синюю кнопку **Оплатить сейчас** в левом нижнем углу страницы.
    ![Снимок экрана, на котором выбрана кнопка "Оплатить сейчас"](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Изменение оплаты по умолчанию для внешних служб

При покупке внешней службы вы выбираете для ресурса подписку Azure. Метод оплаты выбранной подписки Azure станет методом оплаты для внешней службы. Чтобы изменить метод оплаты для внешней службы, необходимо [изменить метод оплаты подписки Azure](../manage/change-credit-card.md), привязанный к этой внешней службе. Выполнив следующие действия можно определить, к какой подписке привязан порядок внешней службы:

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните **Все ресурсы** в меню навигации слева.
     ![снимок экрана: пункт меню "Все ресурсы"](./media/understand-azure-marketplace-charges/all-resources.png)
1. Найдите необходимую внешнюю службу.
1. Найдите название подписки в столбце **Подписка**.
    ![снимок экрана: название подписки на ресурс](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Щелкните название подписки и [обновите активный метод оплаты](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Отмена заказа внешней службы

Если вы хотите отменить заказ внешней службы, то удалите соответствующий ресурс на [портале Azure](https://portal.azure.com).

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните **Все ресурсы** в меню навигации слева.
    ![Снимок экрана: пункт меню "Все ресурсы"](./media/understand-azure-marketplace-charges/all-resources.png)
1. Найдите необходимую внешнюю службу.
1. Установите флажок рядом с ресурсом, который необходимо удалить.
1. На панели команд выберите **Delete** (Удалить).
    ![Снимок экрана: кнопка "Delete" (Удалить)](./media/understand-azure-marketplace-charges/delete-button.png)
1. Введите *"Да"* в колонке подтверждения.
    ![Удаление ресурса](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Щелкните **Удалить**.

## <a name="check-billing-account-type"></a>Проверка типа учетной записи выставления счетов
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия
- [Начало анализа затрат](../costs/quick-acm-cost-analysis.md)