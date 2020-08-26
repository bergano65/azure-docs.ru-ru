---
title: Переход с API EA на API Клиентского соглашения Майкрософт — Azure
description: Эта статья поможет вам понять последствия перехода с Соглашения Microsoft Enterprise на Клиентское соглашение Майкрософт.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 811b2cb7fd9a4f664e7a643f5a8e192a51416888
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689105"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Переход с API EA на API Клиентского соглашения Майкрософт

Эта статья поможет разобраться в структуре данных, API и других различиях системной интеграции между учетными записями Enterprise Agreement (EA) и учетными записями Клиентского соглашения Майкрософт (MCA). Служба управления затратами поддерживает API для обоих типов учетных записей. Прежде чем продолжить, ознакомьтесь со статьей [Настройка учетных записей выставления счетов для Клиентского соглашения Майкрософт](../manage/mca-setup-account.md)

Специалисты организаций с имеющейся учетной записью EA должны ознакомиться с этой статьей перед настройкой учетной записи Клиентского соглашения Майкрософт. Ранее для обновления учетной записи EA и перехода от старой регистрации к новой требовалось минимум шагов. Но для перехода на учетную запись Клиентского соглашения Майкрософт требуются дополнительные усилия. Дополнительные шаги обусловлены изменениями в базовой подсистеме выставления счетов, которые влияют на все API, связанные с затратами, и предложениями услуг.

## <a name="mca-apis-and-integration"></a>API Клиентского соглашения Майкрософт и интеграция

API Клиентского соглашения Майкрософт и новая интеграция позволяют:

- обеспечить полную доступность API через собственные API Azure;
- настроить несколько счетов в одной учетной записи выставления счетов;
- обеспечить доступ к комбинированному API с данными об использовании служб Azure, решений сторонних разработчиков и покупок на Marketplace;
- просматривать затраты в профилях выставления счетов (так же, как и при регистрации) с помощью Управления затратами Azure.
- Получить доступ к новым API для отображения затрат, получения уведомлений о превышении заранее определенных пороговых значений и автоматического экспорта необработанных данных.

## <a name="migration-checklist"></a>Контрольный список действий по миграции

Следующие шаги помогут при переходе на API Клиентского соглашения Майкрософт.

- Ознакомьтесь с новой [учетной записью выставления счетов по Клиентскому соглашению Майкрософт](../understand/mca-overview.md).
- Определите, какие API вы используете, и посмотрите, какие из них заменяются в следующем разделе.
- Ознакомьтесь с [API Azure Resource Manager](/rest/api/azure).
- Если вы еще не используете API Azure Resource Manager, [зарегистрируйте клиентское приложение в Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Обновите код программирования, чтобы в нем [использовалась проверка подлинности Azure AD](/rest/api/azure/#create-the-request).
- В программном коде замените вызовы API EA на вызовы API Клиентского соглашения Майкрософт.
- Обновите механизм обработки ошибок так, чтобы он использовал новые коды ошибок.
- Ознакомьтесь с необходимыми действиями в дополнительных предложениях по интеграции, такими как Cloudyn и Power BI.

## <a name="ea-apis-replaced-with-mca-apis"></a>API EA, замененные на API Клиентского соглашения Майкрософт

Для проверки подлинности и авторизации API EA используют ключ API. API Клиентского соглашения Майкрософт используют проверку подлинности Azure AD.

| Назначение | API EA | API Клиентского соглашения Майкрософт |
| --- | --- | --- |
| Остаток на счете и кредиты. | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Использование (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Использование (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Использование Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Расчетный период | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| прейскурант; | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Покупки резервирований | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Рекомендации по резервированию | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Использование резервирования | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Использование служб Azure и решений сторонних производителей доступны в [API сведений о потреблении](/rest/api/consumption/usagedetails)

Для выставления счетов в рамках Клиентского соглашения Майкрософт доступны следующие API:

| Назначение | API Клиентского соглашения Майкрософт |
| --- | --- |
| Учетные записи выставления счетов<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Профили выставления счетов<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Разделы счетов<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Счета | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Подписки для выставления счетов | {scope}/billingSubscriptions |

<sup>2</sup> API возвращают списки объектов, которые являются областями, в которых выполняются операции управления затратами на портале Azure и в API. Дополнительные сведения см. в статье [Understand and work with scopes](understand-work-scopes.md) (Основные сведения об областях и работе с ними).

При использовании существующих API EA их необходимо обновить, чтобы обеспечить поддержку учетных записей выставления счетов Клиентского соглашения Майкрософт. В следующей таблице показаны другие изменения интеграции.

| Назначение | Старое предложение | Новое предложение |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Управление затратами Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Пакет содержимого и соединитель для аналитики потребления Майкрософт](/power-bi/desktop-connect-azure-consumption-insights) |  [Соединитель для аналитики потребления Майкрософт](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API для получения сведений об остатке на счете и кредитах

API [получения сводной информации о балансе](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) предоставляет ежемесячную сводку по следующим параметрам:

- сведения о балансе.
- покупки;
- плата за обслуживание Azure Marketplace;
- Корректировка
- расходы в связи с избыточным обслуживанием.

Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST).

API получения сводной информации об остатке на счете заменяется Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API.

Чтобы получить сведения о доступных остатках с помощью соответствующего API, используйте код ниже:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API для получения затрат и использования

Ежедневную разбивку затрат на использование служб Azure, сторонних приложений Marketplace и других покупок на Marketplace можно получить с помощью следующих API. Отдельные API объединены для использования служб Azure и сторонних приложений на Marketplace. Старые API заменяются API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). В нем добавлены сведения о покупках на Marketplace, которые ранее отображались только в сводке об остатке на счете на текущую дату.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST).

Все предыдущие API заменяются API сведений о потреблении и использовании.

Чтобы получить сведения об использовании в API сведений об использовании, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

API сведений об использовании, как и все API Управления затратами, доступен в нескольких областях. Для выставленных счетов, которые вы традиционно получаете на уровне регистрации, используйте область профиля выставления счетов.  Дополнительные сведения см. в статье [Understand and work with scopes](understand-work-scopes.md) (Основные сведения об областях и работе с ними).

| Тип | Формат идентификатора |
| --- | --- |
| учетная запись выставления счетов; | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Профиль выставления счетов | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| группа ресурсов. | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Обновите программный код следующими параметрами строки запроса:

| Старые параметры | Новые параметры |
| --- | --- |
| `billingPeriod={billingPeriod}` | Не поддерживается |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Текст ответа также изменился.

Старый текст ответа:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Новый текст ответа:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Имя свойства, содержащего массив записей об использовании, изменилось с данных на _значения_. Каждая запись используется для получения неструктурированного списка подробных свойств. Тем не менее каждая запись теперь находится во вложенном свойстве с именем _свойства_ за исключением тегов. Новая структура согласуется с другими API Azure. Изменились имена некоторых свойств. В таблице ниже приведены соответствующие свойства.

| Старое свойство | Новое свойство | Примечания |
| --- | --- | --- |
| accountId | Недоступно | Создатель подписки не отслеживается. Используйте свойство invoiceSectionId (аналогично departmentId). |
| AccountNameAccountOwnerId и AccountOwnerEmail | Недоступно | Создатель подписки не отслеживается. Используйте свойство invoiceSectionName (аналогично departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Обратите внимание, что эти свойства являются противоположными. Если у свойства isAzureCreditEnabled значение true, у ChargesBilledSeparately будет false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Точные строковые значения могут отличаться. |
| consumedServiceId | None | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date и usageStartDate | Дата | &nbsp;  |
| День | None | Анализирует день в дате. |
| DepartmentId | invoiceSectionId | Точные значения отличаются. |
| DepartmentName | invoiceSectionName | Точные строковые значения могут отличаться. При необходимости настройте соответствие подразделениям в разделах счета. |
| ExtendedCost затраты и Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| IsRecurringCharge | None | &nbsp;  |
| Location | location | &nbsp;  |
| MeterCategory | meterCategory | Точные строковые значения могут отличаться. |
| Значение MeterId | meterId | Точные строковые значения отличаются. |
| MeterName | meterName | Точные строковые значения могут отличаться. |
| MeterRegion | meterRegion | Точные строковые значения могут отличаться. |
| MeterSubCategory | meterSubCategory | Точные строковые значения могут отличаться. |
| Месяц | None | Анализирует месяц в дате. |
| Название предложения | None | Используйте publisherName и productOrderName. |
| offerID | None | &nbsp;  |
| Номер заказа | None | &nbsp;  |
| partNumber | None | Для уникальной идентификации цен используйте meterId и productOrderName. |
| Имя плана | productOrderName | &nbsp;  |
| Продукт | Продукт |   |
| ProductId | productId | Точные строковые значения отличаются. |
| Имя издателя | publisherName | &nbsp;  |
| ResourceGroup | имя_группы_ресурсов | &nbsp;  |
| resourceGuid | meterId | Точные строковые значения отличаются. |
| ResourceLocation | resourceLocation | &nbsp;  |
| resourceLocationId | None | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Недоступно | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Точные строковые значения могут отличаться. |
| serviceTier | meterSubCategory | Точные строковые значения могут отличаться. |
| StoreServiceIdentifier | Недоступно | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Параметр SubscriptionName | subscriptionName | &nbsp;  |
| Теги | tags | Свойство tags применяется к корневому объекту, а не к свойству вложенных свойств. |
| UnitOfMeasure | unitOfMeasure | Точные строковые значения отличаются. |
| usageEndDate | Дата | &nbsp;  |
| Год | None | Анализирует год в дате. |
| (новое) | billingCurrency | Валюта, используемая для оплаты. |
| (новое) | billingProfileId | Уникальный идентификатор для профиля выставления счетов (аналогичный идентификатору регистрации). |
| (новое) | billingProfileName | Имя профиля выставления счетов (аналогично имени регистрации). |
| (новое) | ChargeType | Используйте, чтобы различать использования службы Azure, Marketplace и покупок. |
| (новое) | invoiceId | Уникальный идентификатор счета. Пуст для текущего открытого месяца. |
| (новое) | publisherType | Тип издателя для покупок. Пуст для использования. |
| (новое) | serviceFamily | Тип покупки. Пуст для использования. |
| (новое) | servicePeriodEndDate | Конечная дата для приобретенной службы. |
| (новое) | servicePeriodStartDate | Начальная дата для приобретенной службы. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API периодов выставления счетов заменен API счетов

При выставлении счетов в рамках Клиентского соглашения Майкрософт не используются периоды выставления счетов. Вместо этого используются счета, чтобы ограничить расходы в определенные периоды выставления счетов. [API периодов выставления счетов](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) заменен API счетов. Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST).

Чтобы получить счета с помощью API счетов:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API прейскурантов

В этом разделе описаны существующие API прейскурантов и предоставляются рекомендации по переходу на API прейскурантов для Клиентских соглашений Майкрософт. Здесь также описывается API прейскурантов для Клиентских соглашений Майкрософт и поля прейскурантов. API [получения прейскурантов Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) и [получения периодов выставления счетов Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) заменены API прейскурантов для Клиентских соглашений Майкрософт (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). Новый API поддерживает форматы JSON и CSV в форматах асинхронных запросов REST. Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST).

### <a name="billing-enterprise-apis"></a>API для выставления счетов Enterprise

Вы использовали API для выставления счетов с регистрацией Enterprise, чтобы получать сведения о ценах и периодах выставления счетов. Проверка подлинности и авторизация выполняются с использованием веб-маркеров Azure Active Directory.

Чтобы получить применимые цены для указанного Соглашения о регистрации Enterprise с помощью API прейскурантов и периодов выставления счетов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API прейскурантов для Клиентских соглашений Майкрософт

Используйте API прейскурантов для Клиентских соглашений Майкрософт, чтобы просматривать цены для всех используемых служб Azure и Marketplace. Цены, указанные для профиля выставления счетов, применяются ко всем подпискам, принадлежащим к профилю.

Используйте API прейскурантов для просмотра всех данных прейскурантов используемых служб Azure в формате CSV:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Используйте API прейскурантов для просмотра всех данных прейскурантов для используемых служб Azure в формате JSON:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Использование API возвращает прейскурант для всей учетной записи. Можно также получить сокращенную версию прейскуранта в формате PDF. Сводка включает в себя расходы на использование служб Azure и Marketplace, которые вносятся для конкретного счета. Счет определяется с помощью идентификатора {invoiceId}, который совпадает с **номером счета** в PDF-файлах сводки по счетам. Ниже приведен пример.

![Пример изображения, в котором показан номер счета, соответствующий invoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Чтобы просмотреть информацию в формате CSV по счету с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Чтобы просмотреть информацию в формате JSON по счету с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Вы также можете просмотреть расчетные цены для любой службы потребления Azure или службы потребления Marketplace в текущем открытом периоде выставления счетов или периоде обслуживания.

Чтобы просмотреть расчетные цены на используемые службы в формате CSV с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Чтобы просмотреть расчетные цены на используемые службы в формате JSON с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

API прейскурантов Клиентского соглашение Майкрософт — это *асинхронные REST API*. Ответы для API изменились по сравнению с ответами для старых синхронных API. Текст ответа API также изменился.

#### <a name="old-response-body"></a>Старый текст ответа

Ниже приведен пример ответа синхронного REST API.

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Новый текст ответа

API поддерживают формат [асинхронного REST API Azure](../../azure-resource-manager/management/async-operations.md). При вызове API с помощью GET вы получите следующий ответ:

```
No Response Body

HTTP Status 202 Accepted
```

Отправляются следующие заголовки с расположением выходных данных:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Выполните еще один вызов GET в расположение. Ответ на вызов GET остается тем же, пока операция не будет выполнена или не завершится сбоем. По завершении в ответе на вызов GET возвращает URL-адрес скачивания. Точно так же, как если бы операция выполнялась в то же время. Ниже приведен пример:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Клиент также может выполнить вызов GET для `Azure-AsyncOperation`. Конечная точка возвращает состояние операции.

В следующей таблице показаны поля в API получения прейскурантов Enterprise. Они включают в себя соответствующие поля в новом прейскуранте для Клиентских соглашений Майкрософт:

| Старое свойство | Новое свойство | Примечания |
| --- | --- | --- |
| billingPeriodId  | _Неприменимо_ | Неприменимо. Для Клиентских соглашений Майкрософт счет и связанный с ним прейскурант заменены billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Точные строковые значения могут отличаться. |
| includedQuantity  | includedQuantity | Неприменимо для служб в рамках Клиентских соглашений Майкрософт. |
| partNumber  | _Неприменимо_ | Вместо этого используйте сочетание свойств productOrderName (аналогично offerID) и meterID. |
| unitPrice  | unitPrice | Цена за единицу применима для служб, потребляемых в рамках Клиентских соглашений Майкрософт. |
| currencyCode  | pricingCurrency | В рамках Клиентских соглашениях Майкрософт цены представлены в валюте ценообразования и валюте выставления счетов. currencyCode соответствует свойству pricingCurrency в Клиентских соглашениях Майкрософт. |
| offerID | productOrderName | Вместо offerID можно использовать свойство productOrderName, но оно отличается от offerID. productOrderName и единицы измерения стоимости в Клиентских соглашениях Майкрософт связаны с meterId и offerID в устаревших регистрациях. |

## <a name="consumption-price-sheet-api-operations"></a>Операции API прейскурантов потребления

Для Соглашений Enterprise вы использовали операции API прейскурантов потребления [Get](/rest/api/consumption/pricesheet/get) и [получения периода выставления счетов](/rest/api/consumption/pricesheet/getbybillingperiod) для области по subscriptionId или расчетному периоду. API использует проверку подлинности управления ресурсами Azure.

Чтобы получить сведения о прейскуранте для области с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Чтобы получить сведения о прейскуранте по периоду выставления счетов с помощью API прейскурантов, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Вместо указанных выше конечных точек API используйте следующее для Клиентских соглашений Майкрософт:

**API прейскурантов для Клиентских соглашений Майкрософт (асинхронные REST API)** .

Этот API предназначен для Клиентских соглашений Майкрософт и предоставляет дополнительные атрибуты.

**Прейскурант для области профиля выставления счетов в учетной записи выставления счетов**

Этот API является существующим. Он обновлен и предоставляет прейскуранты для профиля выставления счетов в учетной записи выставления счетов.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Прейскурант для области по учетной записи выставления счетов

Проверка подлинности Azure Resource Manager используется при получении прейскуранта в области регистрации в учетной записи выставления счетов.

Чтобы получить прейскурант для учетной записи регистрации в учетной записи выставления счетов, выполните следующие действия:

| Метод | URI запроса |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Для Клиентского соглашения Майкрософт используйте сведения, приведенные в следующем разделе. Здесь указываются свойства полей, используемые для Клиентских соглашений Майкрософт.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Прейскурант для области профиля выставления счетов в учетной записи выставления счетов

Обновленный API прейскурантов учетной записи выставления счетов получает прейскурант в формате CSV. Чтобы получить прейскурант в области профиля выставления счетов для Клиентского соглашения Майкрософт, сделайте следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

В области регистрации EA ответ и свойства API идентичны. Свойства соответствуют аналогичным свойствам Клиентского соглашения Майкрософт.

Более старые свойства для [API прейскурантов Azure Resource Manager](/rest/api/consumption/pricesheet) и новые свойства приведены в следующей таблице.

| Старое свойство API прейскурантов Azure Resource Manager  | Новое свойство API прейскурантов для Клиентского соглашения Майкрософт   | Description |
| --- | --- | --- |
| Идентификатор средства измерения | _meterId_ | Уникальный идентификатор для средства измерения. Аналогично свойству meterID. |
| Название единицы измерения | meterName | Имя индикатора. Единица измерения представляет собой развертываемый ресурс службы Azure. |
| Категория единицы измерения  | служба | Имя категории классификации для единицы измерения. Аналогично службе в прейскуранте Клиентского соглашения Майкрософт Точные строковые значения отличаются. |
| Подкатегория единицы измерения | meterSubCategory | Имя категории подклассификации единицы измерения. Основано на классификации дифференциации набора признаков высокого уровня в службе. Например, базы данных SQL ценовых категорий "Базовый" и "Стандартный". |
| Регион единицы измерения | meterRegion | &nbsp;  |
| Единицы | _Неприменимо_ | Можно проанализировать из свойства unitOfMeasure. |
| Единица измерения | unitOfMeasure | &nbsp;  |
| Артикул | _Неприменимо_ | Вместо артикула используйте productOrderName и meterID для уникальной идентификации цен в профиле выставления счетов. Поля перечислены в счете Клиентского соглашения Майкрософт вместо артикула. |
| Цена за единицу | unitPrice | Цена за единицу Клиентского соглашения Майкрософт. |
| Код валюты | pricingCurrency | В Клиентских соглашениях Майкрософт цены представлены в валюте ценообразования и валюте выставления счетов. Код валюты соответствует свойству pricingCurrency в Клиентских соглашениях Майкрософт. |
| Включенный объем | includedQuantity | Неприменимо для служб Клиентских соглашений Майкрософт. Отображаются нулевые значения. |
|  Offer ID (Идентификатор предложения)  | productOrderName | Вместо offerID используйте свойство productOrderName. Тем не менее productOrderName (отличное от свойства OfferID) и единица измерения определяют цены в Клиентских соглашениях Майкрософт. Относится к meterId и offerID в устаревших регистрациях. |

Цена для Клиентских соглашений Майкрософт определяется не так, как в соглашениях Enterprise. Цена за использование служб в рамках регистрации Enterprise уникальна для продукта, артикула, единицы измерения и предложения. Артикул не используется в Клиентских соглашениях Майкрософт.

Цена за использование службы потребления ресурсов Azure, которая является частью Клиентского соглашения Microsoft, уникальна для свойств productOrderName и meterID. Они представляют собой единицы измерения службы и план продукта.

Чтобы согласовать прейскурант и использование в API сведений об использовании, можно использовать свойства productOrderName и meterID.

Пользователи с правами владельца профиля выставления счетов, участника, читателя и менеджера счетов могут скачать прейскурант.

Прейскурант содержит цены на службы, стоимость которых основана на использовании. Эти службы включают в себя потребление ресурсов Azure и Marketplace. Последняя цена в конце каждого периода обслуживания блокируется и применяется к использованию в течение одного периода обслуживания. Для служб потребления ресурсов Azure период обслуживания обычно является календарным месяцем.

### <a name="retired-price-sheet-api-fields"></a>Устаревшие поля API прейскурантов

Следующие поля недоступны в API прейскурантов Клиентского соглашения Майкрософт или совпадают.

|Устаревшее поле| Description|
|---|---|
| billingPeriodId | Не применимо. Соответствует InvoiceId для MCA. Соответствует invoiceId для Клиентского соглашения Майкрософт. |
| offerID | Неприменимо. Соответствует productOrderName для Клиентских соглашений Майкрософт. |
| meterCategory  | Неприменимо. Соответствует Service для Клиентских соглашений Майкрософт. |
| unit | Неприменимо. Можно проанализировать из свойства unitOfMeasure. |
| currencyCode | Аналогично свойству pricingCurrency для Клиентских соглашений Майкрософт. |
| meterLocation | Аналогично свойству meterRegion для Клиентских соглашений Майкрософт. |
| Артикул partNumber | Не применяется, так как артикул не указан в счетах для Клиентских соглашений Майкрософт. Вместо артикула используйте сочетание meterId и productOrderName для уникальной идентификации цен. |
| totalIncludedQuantity | Неприменимо. |
| pretaxStandardRate  | Неприменимо. |

## <a name="reservation-instance-charge-api-replaced"></a>Заменен API стоимости резервирования экземпляра

Вы можете получить транзакции выставления счетов на покупку резервирования с помощью [API стоимости резервирования экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Новый API включает в себя все покупки, в том числе сторонних предложений Marketplace. Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST). API стоимости резервирования экземпляра заменяется API транзакций.

Чтобы получить транзакции покупки зарезервированных экземпляров с помощью API транзакций, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Заменены API рекомендаций

API рекомендаций по покупке зарезервированных экземпляров предоставляет сведения об использовании виртуальных машин за последние 7, 30 или 60 дней. API также предоставляют рекомендации по приобретению зарезервированных экземпляров. в том числе:

- [API рекомендаций по резервированию общих экземпляров](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations).
- [API рекомендаций по резервированию отдельных экземпляров](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations).

Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST). Ранее указанные API рекомендаций по резервированию заменены API [Microsoft.Consumption/reservationRecommendation](/rest/api/consumption/reservationrecommendations/list).

Чтобы получить рекомендации по резервированию с помощью API рекомендаций, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Заменены API использования резервирования

Вы можете получить сведения об использовании резервирования в регистрации с помощью API использования зарезервированных экземпляров. Если в регистрации есть несколько зарезервированных экземпляров, можно также получить сведения об использовании всех купленных зарезервированных экземпляров с помощью этого API.

в том числе:

- [Данные об использовании зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Сводка об использовании зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Все API потребления заменены собственными API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове REST API Azure см. в статье [Getting started with REST](/rest/api/azure/#create-the-request) (Начало работы с REST). Ранее указанные API рекомендаций по резервированию заменяются API [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) и [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Чтобы получить сведения о резервировании с помощью API сведений о резервировании, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Чтобы получить сведения о резервировании с помощью API сводок о резервировании, выполните следующее:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Переход от Cloudyn к Управлению затратами

Организации, использующие [Cloudyn](https://cloudyn.com), должны начать использовать [Управление затратами Azure](https://azure.microsoft.com/services/cost-management/) для любых операций по управлению затратами. Управление затратами доступно на портале Azure без адаптации, но с 8-часовой задержкой. Дополнительные сведения см. в [документации по управлению затратами](../index.yml).

В службе "Управление затратами Azure" доступны следующие действия:

- Просматривайте затраты с течением времени по предопределенному бюджету. Выполняйте анализ с помощью шаблонов ежедневных затрат, чтобы выявлять и предотвращать аномальные расходы. Разбивайте затраты по тегам, группам ресурсов, службам и расположениям.
- Создавайте бюджеты с ограничениями на использование и затраты и уведомлениями при приближении важных пороговых значений. Настройка автоматизации с помощью групп действий для запуска пользовательских событий и применения жестких ограничений на ваши условия
- Оптимизируйте затраты и использование благодаря рекомендациям Помощника по Azure. Изучайте данные об оптимизации покупок с резервированием, уменьшайте размер недостаточно используемых виртуальных машин и удаляйте неиспользуемые ресурсы, чтобы соблюдать бюджет.
- Планируйте экспорт данных о затратах и использовании для ежедневной публикации CSV-файла в вашей учетной записи хранения. Автоматизируйте интеграцию с внешними системами для синхронизации и обновления данных выставления счетов.

## <a name="power-bi-integration"></a>Интеграция с Power BI

Для отчетов о затратах также можно использовать Power BI. Соединитель [Управления затратами Azure](/power-bi/desktop-connect-azure-cost-management) для Power BI Desktop можно использовать для создания эффективных настраиваемых отчетов, которые помогут вам лучше управлять своими расходами в Azure. Соединитель Управления затратами Azure сейчас поддерживается для Клиентских соглашений Майкрософт или Соглашений Enterprise.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [документацией по управлению затратами](../index.yml), чтобы узнать, как отслеживать расходы в Azure и управлять ими. С помощью Управления затратами также можно оптимизировать использование ресурсов.
