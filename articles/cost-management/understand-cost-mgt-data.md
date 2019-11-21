---
title: Общие сведения о данных Управления затратами Azure | Документация Майкрософт
description: This article helps you better understand data that's included in Azure Cost Management and how frequently it's processed, collected, shown, and closed.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 25a20ca40b517209fca7b969e3a7399a1c7c7387
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229765"
---
# <a name="understand-cost-management-data"></a>Интерпретация данных службы "Управление затратами"

This article helps you better understand Azure cost and usage data that's included in Azure Cost Management. It explains how frequently data is processed, collected, shown, and closed. Плата за использование Azure будет выставляться ежемесячно. Although billing cycles are monthly periods, cycle start and end dates vary by subscription type. Частота получения данных об использовании в службе "Управление затратами" зависит от различных факторов, например время, необходимое для обработки данных, и как часто службы Azure отправляют данные о потреблении в систему выставления счетов.

Cost Management includes all usage and purchases, including reservations and third-party offerings for Enterprise Agreement (EA) accounts. Microsoft Customer Agreement accounts and individual subscriptions with pay-as-you-go rates  only include usage from Azure and Marketplace services. Support and other costs are not included. Costs are estimated until an invoice is generated and do not factor in credits.

## <a name="supported-microsoft-azure-offers"></a>Supported Microsoft Azure offers

Ниже приведены поддерживаемые в настоящее время [предложения Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) в службе "Управление затратами Azure". Предложение Azure — это тип имеющейся подписки Azure. Data is available in Cost Management starting on the **Data available from** date. If a subscription changes offers, costs before the offer change date will not be available.

| **Категория**  | **Название предложения** | **Quota ID** | **Номер предложения** | **Data available from** |
| --- | --- | --- | --- | --- |
| **Azure для государственных организаций** | Azure для государственных организаций                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | May 2014<sup>1</sup> |
| **Соглашение Enterprise (EA)** | Разработка и тестирование Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | May 2014<sup>1</sup> |
| **Соглашение Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | May 2014<sup>1</sup> |
| **Клиентское соглашение Майкрософт** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Н/Д | March 2019<sup>3</sup> |
| **Клиентское соглашение Майкрософт** | [Microsoft Azure Plan for Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Н/Д | March 2019<sup>3</sup> |
| **Microsoft Customer Agreement supported by partners** | План Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01, and CSPDEVTEST_2018-05-01<br><br>The quota ID is reused for Microsoft Customer Agreement and legacy CSP subscriptions. Currently, only Microsoft Customer Agreement subscriptions are supported. | Н/Д | Октябрь 2019 г. |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | [Разработка и тестирование с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | [Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | October 2, 2018<sup>2</sup> |
| **Оплата по мере использования** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | October 2, 2018<sup>2</sup> |

_<sup>**1**</sup> For data before May 2014, visit the [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2**</sup> For data before October 2, 2018, visit the [Azure Account Center](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft Customer Agreements started in March 2019 and do not have any historical data before this point._

_<sup>**4**</sup> Historical data for credit-based and pay-in-advance subscriptions might not match your invoice. See [Historical data may not match invoice](#historical-data-might-not-match-invoice) below._

The following offers are not supported yet:

| Категория  | **Название предложения** | **Quota ID** | **Номер предложения** |
| --- | --- | --- | --- |
| **Azure для Германии** | [Azure для Германии с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure для государственных организаций** | Azure для государственных организаций с оплатой по мере использования | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Поставщик облачных решений (CSP)** | Microsoft Azure.                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Поставщик облачных решений (CSP)** | Поставщик облачных решений Azure для государственных организаций                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Поставщик облачных решений (CSP)** | Azure для Германии в CSP для Microsoft Cloud для Германии   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Оплата по мере использования**                 | Azure Starter для учащихся | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Оплата по мере использования** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Оплата по мере использования**                 | [Спонсорское предложение Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Планы поддержки** | Поддержка Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Планы поддержки** | Поддержка Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Планы поддержки** | Поддержка разработчика                   | Default_2014-09-01 | MS-AZR-0043P |
| **Планы поддержки** | Планы поддержки для Германии                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Планы поддержки** | Поддержка Standard Azure для государственных организаций   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Планы поддержки** | Профессиональная прямая поддержка в Azure для государственных организаций | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Планы поддержки** | Поддержка Developer для Azure для государственных организаций  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determine your offer type
Если вы не видите данных для подписки и хотите определить, подпадает ли ваша подписка под поддерживаемые предложения, вы можете проверить, поддерживается ли ваша подписка. Чтобы проверить, поддерживается ли ваша подписка Azure, войдите на [портал Azure](https://portal.azure.com). Затем в меню слева выберите **Все службы**. В списке служб выберите **Подписки**. В меню списка подписки щелкните подписку, которую нужно проверить. Подписки отображаются на вкладке "Обзор". Вы увидите **Предложение** и **Идентификатор предложения**. Пример приведен на следующем рисунке.

![Пример вкладки "Обзор подписки", показывающей предложение и идентификатор предложения](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Затраты, включенные в службу "Управление затратами"

В следующих таблицах показаны данные, которые включены (или не включены) в службу "Управление затратами". All costs are estimated until an invoice is generated. Costs shown do not include free and prepaid credits.

**Данных о затратах и использовании**

| **Включено** | **Не включено** |
| --- | --- |
| Azure service usage<sup>5</sup>        | Поддержки расходов. Дополнительные сведения см. в статье [Значение терминов в счете Microsoft Azure](../billing/billing-understand-your-invoice.md). |
| Marketplace offering usage<sup>6</sup> | Налоги. Дополнительные сведения см. в статье [Значение терминов в счете Microsoft Azure](../billing/billing-understand-your-invoice.md). |
| Marketplace purchases<sup>6</sup>      | Кредиты. Дополнительные сведения см. в статье [Значение терминов в счете Microsoft Azure](../billing/billing-understand-your-invoice.md). |
| Reservation purchases<sup>7</sup>      |  |
| Amortization of reservation purchases<sup>7</sup>      |  |

_<sup>**5**</sup> Azure service usage is based on reservation and negotiated prices._

_<sup>**6**</sup> Marketplace purchases are not available for Pay-As-You-Go, MSDN, and Visual Studio offers at this time._

_<sup>**7**</sup> Reservation purchases are only available for Enterprise Agreement (EA) accounts at this time._

**Metadata**

| **Включено** | **Не включено** |
| --- | --- |
| Resource tags<sup>8</sup> | Теги группы ресурсов |

_<sup>**8**</sup> Resource tags are applied as usage is emitted from each service and aren't available retroactively to historical usage._

## <a name="rated-usage-data-refresh-schedule"></a>Расписание обновления данных об использовании

Данные о затратах и потреблении доступны в службе "Управление затратами", а счета выставляются на портале Azure и в [поддерживаемых API](index.yml). Помните о следующих моментах при проверке затрат:

- Оценки расходов за текущий расчетный период обновляются шесть раз в день.
- Ожидаемые затраты за текущий расчетный период могут измениться при большем потреблении.
- Каждое обновление является накопительным и включает в себя все элементы строки и информацию из предыдущего обновления.
- Azure завершает или _закрывает_ текущий расчетный период в течение 72 часов (трех календарных дней) после завершения расчетного периода.

В следующих примерах показано, как могут заканчиваться расчетные периоды.

Подписки Enterprise Agreement (EA). Если расчетный месяц заканчивается 31 марта, ожидаемые расходы обновляются в течение следующих 72 часов. В этом примере: к полночи (UTC) 4 апреля.

Подписки с оплатой по мере использования. Если расчетный месяц заканчивается 15 мая, ожидаемые расходы могут обновляться в течение следующих 72 часов. В этом примере: к полночи (UTC) 19 мая.

### <a name="rerated-data"></a>Повторно оцененные данные

Whether you use the [Cost Management APIs](index.yml), Power BI, or the Azure portal to retrieve data, expect the current billing period's charges to get rerated, and consequently change, until the invoice is closed.

## <a name="usage-data-update-frequency-varies"></a>Частота обновления данных об использовании может различаться

Доступность данных об использовании в службе управления затратами зависит от нескольких факторов, включая:

- частоту передачи данных о потреблении в службы Azure, такие как Хранилище, Вычисления, CDN и SQL;
- время, необходимое для обработки данных о потреблении с помощью механизма оценки и конвейеров управления затратами.

Некоторые службы отправляют данные об использовании чаще, чем другие. Таким образом, вы можете увидеть данные в службе "Управление затратами" для некоторых служб быстрее, чем для других, которые реже отправляют данные об использовании. Как правило, сведения об использовании отображаются в службе "Управление затратами" в течение 8–24 часов. Имейте в виду, что данные за открытый месяц обновляются по мере увеличения использования, так как обновления являются накопительными.

## <a name="historical-data-might-not-match-invoice"></a>Historical data might not match invoice

Historical data for credit-based and pay-in-advance offers might not match your invoice. Some Azure pay-as-you-go, MSDN, and Visual Studio offers can have Azure credits and advanced payments applied to the invoice. However, the historical data shown in Cost Management is based on your estimated consumption charges only. Cost Management historical data doesn't include payments and credits. As a result, the historical data shown for the following offers may not match exactly with your invoice.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Free Trial (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Дополнительные материалы

- См. первое краткое руководство по [работе с Управлением затратами](quick-acm-cost-analysis.md).
