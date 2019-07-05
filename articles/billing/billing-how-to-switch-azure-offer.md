---
title: Изменение предложения подписки Azure
description: Узнайте о том, как изменить подписку Azure и переключиться на другое предложение, используя Центр учетных записей Azure.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8e87bcb9f7d05fd5041623693c96f8c737758c32
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490985"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Изменить подписку Azure на другое предложение

Клиентом [отдельной подписки с оплатой](https://azure.microsoft.com/offers/ms-azr-0003p/), вы можете переключить свою подписку Azure на другое предложение в [Центр управления учетной записью](https://account.windowsazure.com/Subscriptions). Например, с помощью этой функции вы можете воспользоваться преимуществами [ежемесячных кредитов для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Просто хотите повысить уровень подписки с бесплатной пробной версии?** См. в разделе [Повысьте уровень подписки](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Поддерживаемые функции

Вы можете переключиться из отдельной подписки с оплатой для:

- [Разработка и тестирование с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> По вопросам других вариантов смены предложения [обращайтесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Изменение предложения подписки

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Войдите в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions).
1. Выберите отдельные подписки с оплатой.
1. Щелкните **Переключиться на другое предложение**. Параметр доступен только в том случае, если вы используете отдельные подписки с оплатой и завершения первого расчетного периода.

   ![Обратите внимание на кнопку "Переключить предложение" в правой части страницы.](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Выберите нужное предложение** в списке предложений, на которые можно переключить вашу подписку. Список доступных предложений будет зависеть от членства вашей учетной записи. Если список пуст, проверьте [список доступных предложений, на которые вы можете переключить подписку](#whats-supported), и убедитесь, что ваша учетная запись имеет правильное членство.

   ![Выберите предложение, на которое нужно переключиться.](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. При переходе на некоторые предложения может появляться уведомление о последствиях перехода. Внимательно ознакомьтесь с этим списком и выполните указанные действия, прежде чем продолжить.

   ![Просмотрите примечания.](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Вы можете переименовать подписку. По умолчанию он будет присвоено имя нового предложения. Чтобы завершить процедуру, щелкните **Переключить предложение** .

   ![Нажмите зеленую кнопку.](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Готово! Подписка переведена на новое предложение.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
Ниже разделах даны ответы на часто задаваемые вопросы.

### <a name="what-is-an-azure-offer"></a>Что такое предложение Azure?

Предложение Azure — это *тип* имеющейся подписки Azure. Например [подписку с оплатой](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure с открытой лицензией](https://azure.microsoft.com/offers/ms-azr-0111p/), и [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) все предложения Azure. Для каждого предложения определены разные [условия](https://azure.microsoft.com/support/legal/offer-details/), а в некоторых предложениях предусмотрены особые преимущества. Предложение для подписки можно найти на странице подписки Центра управления учетной записью. Щелкните имя предложения, чтобы получить дополнительные сведения.

   ![Щелкните ссылку "Предложение" в Центре управления учетной записью, чтобы получить дополнительные сведения.](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Почему кнопка не отображается?

Могут отобразиться не **переключиться на другое предложение** параметр, если:

* У вас нет [подписки с оплатой](https://azure.microsoft.com/offers/ms-azr-0003p/). Можно преобразовать в настоящее время только подписки с оплатой на другое предложение.
  * Если у вас есть [бесплатной пробной версии](https://azure.microsoft.com/free/), узнайте, как [обновление до оплаты по мере использования](billing-upgrade-azure-subscription.md).
  * Чтобы переключить предложение из другой подписки, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Вы по-прежнему в ваш первый расчетный период; необходимо дождаться первого расчетного периода до конца, прежде чем вы сможете изменить предложение.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Почему отображается сообщение: "На данный момент предложения для вашей страны или вашего региона отсутствуют"?

* Возможно, у вас нет права на изменение предложения. Проверьте [список доступных предложений, на которые можно перейти](#whats-supported), и убедитесь в том, что у вас активированы правильные преимущества в Visual Studio или BizSpark.
* Некоторые предложения не могут быть доступны во всех странах и регионах.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Как переключение предложений Azure повлияет на мою службу и выставление счетов?

Далее объяснятся, что произойдет, если вы измените предложения Azure в центре управления учетной записью.

#### <a name="no-service-downtime"></a>Нет простоя служб

Для пользователей, связанных с подпиской, службы будут работать как обычно. Однако предложение, на которое вы переключаетесь, может включать ограничения, Например, некоторые предложения запрещают использование в рабочей среде, поэтому вам нужно будет переместить рабочие ресурсы в другую подписку.

#### <a name="quota-increases-are-reset"></a>Сбрасываются увеличения квот

При изменении предложений сбрасываются все [увеличения ограничений и квот, превышающие значения по умолчанию](../azure-supportability/resource-manager-core-quotas-request.md). При этом не возникает простой служб, даже если объем ресурсов превышает ограничение по умолчанию. Например, если вы используете в своей подписке 200 ядер, то в результате изменения предложения квота на ядра сбрасывается до значения по умолчанию (20 ядер). На работу виртуальных машин, использующих 200 ядер, это не повлияет. Они продолжат работать. Однако, если больше не запрашивать увеличение квоты, то вы не сможете подготовить больше ядер.

#### <a name="billing"></a>Выставление счетов

В день смены предложения выставляется счет за все непогашенные задолженности. После этого счет за подписку будет выставляться по тарифам нового предложения. Дата взыскания годовой абонентской платы изменится в соответствии с датой смены предложения. Данные по платежам и использованию ресурсов до смены предложения не сохраняются, поэтому рекомендуем скачать их копию перед сменой тарифа.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Можно ли перенести из подписки с оплатой для [поставщика облачных решений](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) или [соглашения Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Инструкции по переходу на CSP см. в статье [Migrate Azure subscriptions from pay-as-you-go to Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp) (Перенос подписки Azure из подписки с оплатой по мере использования в Azure CSP).
* Чтобы перейти к предложению "Соглашение Enterprise", попросите своего администратора регистрации добавить вашу учетную запись в соглашение Enterprise. Следуйте инструкциям в электронном сообщении с приглашением, чтобы перевести подписки на регистрацию в соглашении Enterprise. Дополнительные сведения см. в статье [Associate an Existing Account](https://ea.azure.com/helpdocs/associateExistingAccount) (Связывание имеющейся учетной записи) на портале EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Можно ли перенести данные и службы в новую подписку?

* Вы можете перенести ресурсы в новую подписку напрямую. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/resource-group-move-resources.md).
* Чтобы передать права собственности на подписку Azure и на все ресурсы в ней другому пользователю, см. [эту статью](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия
- [Начало анализа затрат](../cost-management/quick-acm-cost-analysis.md)
