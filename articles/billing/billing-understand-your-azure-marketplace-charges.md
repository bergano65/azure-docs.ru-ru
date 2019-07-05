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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490355"
---
# <a name="understand-your-azure-external-services-charges"></a>Основная информация о расходах на внешних служб Azure
Внешние службы публикуются сторонними поставщиками в Azure marketplace. Например SendGrid является внешней службы, которые можно приобрести в Azure, но не публикуется корпорацией Майкрософт. Некоторые продукты корпорации Майкрософт слишком продаются через Azure marketplace.

## <a name="how-external-services-are-billed"></a>Как выставляются счета за внешние службы

- Если у вас есть [соглашении клиента Microsoft](#check-access), сторонних служб взимается с остальной частью служб Azure.
- Если у вас нет клиента соглашение Microsoft, на внешние службы выставляются отдельно из служб Azure.
- У каждой внешней службы своя модель выставления счетов. Некоторые службы тарифицируются в режиме по мере использования, а другие имеют фиксированное ежемесячных выплатах.
- Для оплаты внешних служб невозможно использовать бесплатные кредитные средства. Если вы используете подписку Azure, включающую [бесплатные кредиты](https://azure.microsoft.com/pricing/spending-limits/), их нельзя использовать для оплаты из внешних служб. При подготовке новой внешней службы или ресурса отображается предупреждение.

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

## <a name="view-and-download-invoices"></a>Просматривать и скачивать счета

Если у вас есть [соглашении клиента Microsoft](#check-access), плату за сторонних находятся в одном счете как расходах на Azure. Узнайте, как [просматривать и скачивать счета Azure](billing-download-azure-invoice.md) на портале Azure, чтобы увидеть ориентировочную стоимости независимых производителей.

Если у вас нет клиента соглашение Microsoft, у вас есть отдельные счета для независимых производителей расходов. Можно просматривать и скачивать счета в Azure Marketplace на портале Azure, выполнив следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Поиск **Cost Management + выставление счетов**.
1. В меню слева выберите **счета-фактуры**.
1. Щелкните **Azure Marketplace и резервирование** вкладки.  ![Изображение Azure marketplace и резервирования](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. В раскрывающемся списке подписок выберите подписку, которая содержит внешние службы, чтобы просмотреть счета-фактуры для.

## <a name="external-spending-for-ea-customers"></a>Внешние предельную сумму расходов для клиентов EA

Клиенты с соглашением Enterprise могут просматривать данные о расходах на внешние службы и скачивать отчеты на портале EA. Чтобы приступить к работе, см. статью [Azure Marketplace for EA Customers](https://ea.azure.com/helpdocs/azureMarketplace) (Azure Marketplace для клиентов с соглашением Enterprise).

## <a name="manage-payment-for-external-services"></a>Управление оплаты для внешних служб

При приобретении внешней службы, вы выбираете подписку Azure для ресурса. Метод оплаты подписки Azure, выбранный становится способ оплаты для внешней службы. Чтобы изменить способ оплаты для внешней службы, необходимо выполнить [изменить метод оплаты подписки Azure](billing-how-to-change-credit-card.md) привязаны к этой внешней службы. Можно выяснить, какие подписки принимается равным заказ внешней службы, выполнив следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com).
1. Щелкните **все ресурсы** в левом меню навигации.
     ![Снимок экрана: пункт меню все ресурсы](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Поиск вашей внешней службы.
1. Найдите имя подписки в **подписки** столбца.
    ![Снимок экрана: имя подписки для ресурса](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Щелкните имя подписки и [обновить метод оплаты активным](billing-how-to-change-credit-card.md).

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
1. Щелкните **все ресурсы** в левом меню навигации.
    ![Снимок экрана: пункт меню все ресурсы](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Поиск вашей внешней службы.
1. Установите флажок рядом с ресурс, который требуется удалить.
1. Выберите **удалить** на панели команд.
    ![Снимок экрана кнопки "Удалить"](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Тип *«Да»* в колонке подтверждения.
    ![Удалить ресурс](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Нажмите кнопку **Delete**(Удалить).

## <a name="check-access"></a>Проверки доступа
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или нужна помощь, [создать запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия
- [Начало анализа затрат](../cost-management/quick-acm-cost-analysis.md)
