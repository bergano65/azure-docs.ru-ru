---
title: Основная информация о расходах на внешние службы в Azure | Документация Майкрософт
description: Узнайте больше о выставлении счетов за использование внешних служб, ранее известных как Marketplace, в Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490355"
---
# <a name="understand-your-azure-external-services-charges"></a>Основная информация о расходах на внешние службы в Azure
Внешние службы публикуются сторонними поставщиками программного обеспечения в Azure Marketplace. Например, SendGrid — это внешняя служба, которую можно приобрести в Azure, но которая не опубликована корпорацией Майкрософт. Некоторые продукты Майкрософт также продаются через Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Как выставляются счета за внешние службы

- Если у вас есть [Клиентское соглашение Майкрософт](#check-access), то ваши службы сторонних поставщиков будут оплачиваться вместе с остальными службами Azure.
- Если у вас нет Клиентского соглашения Майкрософт, счета за внешние службы оплачиваются отдельно от служб Azure.
- У каждой внешней службы своя модель выставления счетов. Некоторые службы оплачиваются по мере использования, в то время как для других установлена фиксированная ежемесячная оплата.
- Для оплаты внешних служб невозможно использовать бесплатные кредитные средства. Если вы используете подписку Azure, которая включает в себя [бесплатные деньги на счете](https://azure.microsoft.com/pricing/spending-limits/), то ими невозможно оплачивать счета за внешние службы. При подготовке новой внешней службы или ресурса отображается предупреждение.

    ![Предупреждение о покупке Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Просмотр и скачивание счетов

Если у вас есть [Клиентское соглашение Майкрософт](#check-access), ваши расходы на сторонних поставщиков будут включены в счет, вместе с расходами за Azure. Узнайте, как [просмотреть и скачать свой счет Azure](billing-download-azure-invoice.md) из портала Azure, чтобы узнать о ваших расходах на сторонних поставщиков.

Если у вас нет Клиентского соглашения Майкрософт, вы получите отдельный счет на оплату услуг сторонних поставщиков. Вы можете просмотреть и скачать счета для Azure Marketplace из портала Azure, выполнив следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по фразе **Управление затратами + выставление счетов**.
1. В меню слева выберите **Счета**.
1. Щёлкните вкладку **Azure Marketplace и резервирования**.  ![Изображение вкладки "Azure Marketplace и резервирования"](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. В раскрывающемся списке подписки выберите подписку, содержащую внешние службы, для которых требуется просмотреть счета.

## <a name="external-spending-for-ea-customers"></a>Внешние расходы для клиентов с соглашением Enterprise

Клиенты с соглашением Enterprise могут просматривать данные о расходах на внешние службы и скачивать отчеты на портале EA. Чтобы приступить к работе, см. статью [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace для клиентов с соглашением Enterprise).

## <a name="manage-payment-for-external-services"></a>Управление платежами за внешние службы

При покупке внешней службы вы выбираете для ресурса подписку Azure. Метод оплаты выбранной подписки Azure станет методом оплаты для внешней службы. Чтобы изменить метод оплаты для внешней службы, необходимо [изменить метод оплаты подписки Azure](billing-how-to-change-credit-card.md), привязанный к этой внешней службе. Выполнив следующие действия можно определить, к какой подписке привязан порядок внешней службы:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Щелкните **Все ресурсы** в меню навигации слева.
     ![снимок экрана: пункт меню "Все ресурсы"](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Найдите необходимую внешнюю службу.
1. Найдите название подписки в столбце **Подписка**.
    ![снимок экрана: название подписки на ресурс](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Щелкните название подписки и [обновите активный метод оплаты](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Отмена заказа внешней службы
Если вы хотите отменить заказ внешней службы, то удалите соответствующий ресурс на [портале Azure](https://portal.azure.com).

1. Войдите на [портале Azure](https://portal.azure.com).
1. Щелкните **Все ресурсы** в меню навигации слева.
    ![Снимок экрана: пункт меню "Все ресурсы"](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Найдите необходимую внешнюю службу.
1. Установите флажок рядом с ресурсом, который необходимо удалить.
1. На панели команд выберите **Delete** (Удалить).
    ![Снимок экрана: кнопка "Delete" (Удалить)](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Введите *"Да"* в колонке подтверждения.
    ![Удаление ресурса](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Нажмите кнопку **Delete**(Удалить).

## <a name="check-access"></a>Проверка доступа
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дополнительная информация
- [Начало анализа затрат](../cost-management/quick-acm-cost-analysis.md)
