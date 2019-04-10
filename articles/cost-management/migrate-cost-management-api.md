---
title: Перенос из Enterprise Agreement в соглашение Microsoft Customer интерфейсы API — Azure | Документация Майкрософт
description: Эта статья поможет вам понять последствия перехода на соглашение Microsoft клиента Microsoft Enterprise Agreement (EA).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279879"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Переход с соглашения Enterprise на API-интерфейсы Microsoft Customer соглашения

Эта статья поможет вам понять структуру данных, API и другие различия интеграции системы между учетными записями Enterprise Agreement (EA) и соглашение Майкрософт клиент (MCA). Управление затратами Azure поддерживает API-интерфейсы для обоих типов учетных записей. Просмотрите [выставления счетов учетную запись для установки](../billing/billing-mca-setup-account.md) статье соглашении клиента Microsoft, прежде чем продолжить.

Организации с существующей учетной записи EA должны ознакомьтесь со статьей в сочетании с настройкой учетной записи MCA. Ранее обновление учетной записи EA требуется некоторые минимальных затрат для перемещения из старой регистрации в новую. Тем не менее переход на учетную запись MCA требуются дополнительные усилия. Из-за изменений в базовой подсистемы выставления счетов, которые влияют на все API, связанные с затрат и предложения услуг — дополнительных усилий.

## <a name="mca-apis-and-integration"></a>API-интерфейсы MCA и интеграция

API-интерфейсы MCA и новой интеграции позволяют:

- Иметь полную доступность API через собственные API-интерфейсы Azure.
- Настройте несколько счетов в одной учетной записи выставления счетов.
- Доступ к объединенный интерфейс API с использования службы Azure, использование Marketplace независимых производителей и покупки в Marketplace.
- Просмотр затрат на выставление счетов профилей (аналогично регистрациями) с помощью Azure Cost Management.
- Доступ к API для отображения затраты, получать уведомление, если затраты превышают предопределенными пороговыми значениями и экспорт необработанных данных автоматически.

## <a name="migration-checklist"></a>Контрольный список миграции

В следующих элементов справки при переходе к API-интерфейсы MCA.

- Ознакомьтесь с новым [соглашении клиента Microsoft платежный счет](../billing/billing-mca-overview.md).
- Определите, какие интерфейсы API, вы используете и увидеть, какие из них заменяются в следующем разделе.
- Ознакомьтесь с [API REST диспетчера ресурсов Azure](/rest/api/azure).
- Если вы еще не используете API-интерфейсов Azure Resource Manager, [регистрации клиентского приложения с Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Любой программный код, чтобы обновить [использование аутентификации Azure AD](/rest/api/azure/#create-the-request).
- Обновите любой программный код, чтобы заменить вызовы EA API с вызовами MCA API.
- Обновите обработку ошибок, чтобы использовать новые коды ошибок.
- Проверка интеграции предложениями, как Cloudyn и Power BI, для других требуется действие.

## <a name="ea-apis-replaced-with-mca-apis"></a>API-интерфейсы EA заменены на API MCA

API-интерфейсы EA использовать ключ API для проверки подлинности и авторизации. API-интерфейсы MCA использовать проверку подлинности Azure AD.

| Цель | EA API | MCA API |
| --- | --- | --- |
| Баланс и деньги на счете | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Использование (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Об использовании (CSV) | [/ usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) [ /usagedetails или отправить](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Использование Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Расчетный период | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| прейскурант; | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Формат Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download = json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... Формат /pricesheet/Default/Download = json|CSV Microsoft.Billing/billingAccounts/... / billingProfiles /... /providers/Microsoft.Consumption/pricesheets/Download  |
| Резервирование покупок | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Рекомендации по резервированию | [/ SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Данные о резервировании | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> службы azure и использование стороннего Marketplace доступны с [API сведений об использовании](/rest/api/consumption/usagedetails).

Для учетных записей выставления счетов MCA доступны следующие API:

| Цель | API клиента Microsoft соглашения (MCA) |
| --- | --- |
| Учетные записи для выставления счетов<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Профили счетов<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Счет разделах<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Счета | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Подписки выставления счетов | {scope} / billingSubscriptions |

<sup>2</sup> API-интерфейсы возвращать списки объектов, которые являются областей, где возникает управление затратами на портале Azure, а также API-интерфейсы работают. Дополнительные сведения об областях управления затратами см. в разделе [общие принципы и работы с областями](understand-work-scopes.md).

Если вы используете любые существующие интерфейсы API EA, необходимо обновить их для поддержки учетных записей MCA для выставления счетов. В следующей таблице показаны другие изменения интеграции:

| Цель | Старый предложения | Новое предложение |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Управление затратами Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) содержимого пакета и соединителя | [Приложение Microsoft Azure Consumption Insights для Power BI](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) и [соединитель Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API-интерфейсы, чтобы получить баланс и деньги на счете

[Получить баланс сводки](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API дает ежемесячная Сводка:

- сведения о балансе.
- Новые покупки
- Расходах службы в Azure Marketplace
- Корректировка
- Расходы в связи с избытком для службы

Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request).

API получения Сводка баланса заменяется Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API.

Чтобы получить доступные сальдо с помощью доступных API сальдо:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API для получения затрат и использования

Получение ежедневного разбивка расходов от использования службы Azure, использование сторонних Marketplace и других покупки в Marketplace с помощью следующих API-интерфейсов. Следующие отдельный API было выполнено слияние для служб Azure и использование стороннего Marketplace. Старые API заменяются [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Он добавляет покупки в Marketplace, которые были ранее только отображаются в баланс сводки к дате.

- [Получение сведений об использовании и скачивание](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Получение сведений или отправить использования](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Получение сведений об использовании/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Получение сведений об использовании/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Получите бесплатно магазина marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Получите бесплатно магазина marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request).

Сведения о API использования и потребления, заменяются старых API-интерфейсов.

Чтобы получить сведения об использовании с помощью API сведений об использовании:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Сведения об API использования, как и все интерфейсы API управления затрат, доступен в нескольких областях. Фактурные затрат как обычно вы получали на уровне регистрации, используйте область профиля выставления счетов.  Дополнительные сведения об областях управления затратами см. в разделе [общие принципы и работы с областями](understand-work-scopes.md).

| Тип | Формат идентификатора |
| --- | --- |
| учетная запись выставления счетов; | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Профиль выставления счетов | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Подписка | `/subscriptions/{subscriptionId}` |
| Группа ресурсов | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Используйте следующие параметры строки запроса для обновления кода.

| Прежние параметры | Новые параметры |
| --- | --- |
| `billingPeriod={billingPeriod}` | Не поддерживается |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Текст ответа также изменяется.

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

Имя свойства, содержащему массив записей об использовании изменен на основе данных для _значения_. Каждая запись, используемая для имеют плоский список подробные свойства. Тем не менее, каждая запись теперь все сведения, теперь находятся в вложенное свойство с именем _свойства_, за исключением теги. Новая структура согласуется с других API-интерфейсов Azure. Некоторые имена свойств были изменены. В следующей таблице показаны соответствующие свойства.

| Свойства старого | Новое свойство | Notes |
| --- | --- | --- |
| ИД учетной записи | Н/Д | Создатель подписки не отслеживаются. Используйте invoiceSectionId (аналогично departmentId). |
| AccountNameAccountOwnerId и AccountOwnerEmail | Н/Д | Создатель подписки не отслеживаются. Используйте invoiceSectionName (то же, как «отдел»). |
| Дополнительные сведения | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Обратите внимание на то, что эти свойства являются противоположными. Если isAzureCreditEnabled имеет значение true, ChargesBilledSeparately будет иметь значение false. |
| ConsumedQuantity | quantity | &nbsp; |
| Использованная служба | consumedService | Точное строковые значения могут отличаться. |
| ИД использованной службы | Отсутствует | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Дата и usageStartDate | дата | &nbsp;  |
| День | Отсутствует | Выполняет синтаксический анализ день из даты. |
| ИД отдела | invoiceSectionId | Точные значения отличаются. |
| DepartmentName | invoiceSectionName | Точное строковые значения могут отличаться. Настройте разделы счетов в соответствии с отделами, при необходимости. |
| ExtendedCost и затрат | costInBillingCurrency | &nbsp;  |
| InstanceId | ResourceId | &nbsp;  |
| Является повторяющимся платежом | Отсутствует | &nbsp;  |
| Размещение | расположение | &nbsp;  |
| Категория единицы измерения | meterCategory | Точное строковые значения могут отличаться. |
| Значение MeterId | meterId | Точная строка значения отличаются. |
| Имя единицы измерения | meterName | Точное строковые значения могут отличаться. |
| Регион единицы измерения | meterRegion | Точное строковые значения могут отличаться. |
| Подкатегория единицы измерения | meterSubCategory | Точное строковые значения могут отличаться. |
| Месяц | Отсутствует | Выполняет синтаксический анализ месяц из даты. |
| Название предложения | Отсутствует | Используйте параметры publisherName и productOrderName. |
| OfferId | Отсутствует | &nbsp;  |
| Номер заказа | Отсутствует | &nbsp;  |
| PartNumber | Отсутствует | Используйте meterId и productOrderName для уникальной идентификации цены. |
| Имя плана | productOrderName | &nbsp;  |
| Продукт | Продукт |   |
| ProductId | productId | Точная строка значения отличаются. |
| Имя издателя | publisherName | &nbsp;  |
| ResourceGroup | имя_группы_ресурсов | &nbsp;  |
| ResourceGuid | meterId | Точная строка значения отличаются. |
| Расположение ресурса | resourceLocation | &nbsp;  |
| ИД расположения ресурса | Отсутствует | &nbsp;  |
| Частота ресурса | effectivePrice | &nbsp;  |
| ИД администратора службы | Н/Д | &nbsp;  |
| Служебная информация 1 | serviceInfo1 | &nbsp;  |
| Служебная информация 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Точное строковые значения могут отличаться. |
| Имя категории | meterSubCategory | Точное строковые значения могут отличаться. |
| ИД службы хранилища | Н/Д | &nbsp;  |
| GUID подписки | subscriptionId | &nbsp;  |
| Идентификатор подписки | subscriptionId | &nbsp;  |
| Параметр SubscriptionName | subscriptionName | &nbsp;  |
| Теги | теги | Свойство tags применяется к корневой объект, не к свойству вложенных свойств. |
| Единица измерения | unitOfMeasure | Точная строка значения отличаются. |
| usageEndDate | дата | &nbsp;  |
| Год | Отсутствует | Выполняет синтаксический анализ год с момента. |
| (новая) | billingCurrency | Валюта, используемая для расходов. |
| (новая) | billingProfileId | Уникальный идентификатор для профиля выставления счетов (аналогично регистрации). |
| (новая) | billingProfileName | Имя профиля выставления счетов (аналогично регистрации). |
| (новая) | chargeType | Используйте для разделения служб Azure, использование Marketplace и покупки. |
| (новая) | invoiceId | Уникальный идентификатор счета. Пустая для открытого месяца. |
| (новая) | publisherType | Тип добавляемого издателя, для покупок. Пустым для использования. |
| (новая) | serviceFamily | Тип покупки. Пустым для использования. |
| (новая) | servicePeriodEndDate | Конечная дата для приобретенной службы. |
| (новая) | servicePeriodStartDate | Начальная дата приобретенной службой. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API периоды, заменены счета-фактуры API выставления счетов

Учетные записи для выставления счетов MCA не используйте расчетных периодов. Вместо этого они используют накладные расходы область для определенных периодов выставления счетов. [API расчетных периодов](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) заменяется API счетов. Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request).

Для получения счета-фактуры с помощью API счета-фактуры:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API-интерфейсы таблицы стилей цена

В этом разделе рассматриваются существующие API-интерфейсы Цена таблицы стилей и рекомендации для перемещения API прейскуранта соглашения клиента Microsoft. Также рассматриваются API прейскуранта соглашения Microsoft Customer и описание поля в прайс-листы. [Получить Enterprise прайс-лист](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) и [Enterprise получить расчетных периодов, которые](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API-интерфейсы заменяются API прейскуранта соглашения Microsoft Customer (Microsoft.Billing/billingAccounts/billingProfiles / прайс-лист). Новый API поддерживает JSON и CSV форматы, в форматах асинхронные REST. Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Enterprise интерфейсами API выставления счетов

Вы использовали API выставления счетов Enterprise корпоративные регистрации для получения цены и сведения о выставлении счетов периода. Проверка подлинности и авторизация используется веб-токенов Azure Active Directory.

Чтобы получить применимые цены для определенной регистрации Enterprise с прайс-лист и API период выставления счетов:

| Метод | URI запроса |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API прейскуранта для соглашений клиентов корпорации Microsoft

Используйте API прейскуранта соглашения клиента Microsoft для просмотра цен для всех служб потребления потребления Azure и Marketplace. Указанные цены для выставления счетов профиля применяются ко всем подпискам, которые принадлежат профиль выставления счетов.

Используйте API прейскуранта на отображение всех потребления Azure services прайс-лист данных в формате CSV:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Используйте API прейскуранта на отображение всех потребления Azure services прайс-лист данных в формате JSON:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

С помощью API возвращает прайс-листа для всей учетной записи. Тем не менее можно также получить сокращенное описание прайс-листа в формате PDF. Сводка содержит потребления Azure и Marketplace потребление служб, которые будут выставляться счета за определенного счета. Счет определяется {invoiceId} которого совпадает со значением **номер счета** показано в счете Сводка PDF-файлы. Ниже приведен пример.

![InvoiceId примере изображение номер счета, который соответствует](./media/migrate-cost-management-api/invoicesummary.png)

Для просмотра сведений о счете с API прейскуранта в формате CSV:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Для просмотра сведений о счете с API прейскуранта в формате JSON:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Также вы увидите предполагаемое цены для любой службы потребления потребления Azure или Marketplace в текущего откройте цикла выставления счетов или период обслуживания.

Чтобы просмотреть цены на предполагаемое потребление служб с помощью API прейскуранта в формате CSV:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Чтобы просмотреть цены на предполагаемое потребление служб с помощью API прейскуранта в формате JSON:

| Метод | URI запроса |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft Customer соглашения цена лист API-интерфейсы являются *асинхронные интерфейсы API REST*. Ответы для API-интерфейсы изменено с более старой синхронных API. Текст ответа API также изменяется.

#### <a name="old-response-body"></a>Старый текст ответа

Вот пример синхронной ответа REST API:

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

API-интерфейсы поддерживают [асинхронные REST Azure](../azure-resource-manager/resource-manager-async-operations.md) формат. Вызов API, с помощью запроса GET, и появляется следующий ответ:

```
No Response Body

HTTP Status 202 Accepted
```

Расположение выходных данных отправляются следующие заголовки:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Сделайте другой GET вызов к расположению. Ответ на вызов GET совпадает пока операция достигнет в состояние завершения или сбоя. После завершения в ответ на вызов GET location возвращает URL-адрес загрузки. Так, как если бы операция была выполнена в то же время. Ниже приведен пример:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Клиент также может выполнить вызов GET для `Azure-AsyncOperation`. Конечная точка возвращает состояние для операции.

Следующая таблица отображает поля в более старых получить Enterprise API прейскуранта. Он включает соответствующие поля в новый прайс-лист для соглашений клиентов Microsoft:

| Свойства старого | Новое свойство | Notes |
| --- | --- | --- |
| billingPeriodId  | _неприменимо_ | Не применяется Для соглашений клиентов Microsoft концепция billingPeriodId заменены счета и связанных прайс-листа. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Точное строковые значения могут отличаться. |
| includedQuantity  | includedQuantity | Не применимо для служб в соглашениях Microsoft Customer. |
| partNumber  | _неприменимо_ | Вместо этого используйте сочетание productOrderName (аналогично offerId) и снабжены. |
| unitPrice  | unitPrice | Цена за единицу применяется для служб, которые используются в соглашениях Microsoft Customer. |
| currencyCode  | pricingCurrency | Соглашений клиентов Майкрософт имеют цену представления в ценовой категории валюта и Валюта выставления счетов. CurrencyCode соответствует pricingCurrency в соглашениях Microsoft Customer. |
| offerId | productOrderName | Вместо OfferId вы можете использовать productOrderName, но не совпадает с OfferId. Тем не менее productOrderName и индикатор определения цены в соглашениях Microsoft Customer, относящимися к meterId и Offerid в устаревших регистраций. |

## <a name="consumption-price-sheet-api-operations"></a>Операции API прейскуранта потребления

Для соглашения Enterprise, вы использовали API прейскуранта потребления [получить](/rest/api/consumption/pricesheet/get) и [получить, определенный расчетный период](/rest/api/consumption/pricesheet/getbybillingperiod) операций для области subscriptionId или расчетного периода. Этот API использует проверку подлинности для управления ресурсами Azure.

Для получения сведений прайс-листа для области с API прейскуранта:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Для получения сведений прайс-лист расчетный период с API прейскуранта.

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Вместо приведенные выше конечные точки API используйте приведенные ниже запросы клиентов соглашения Microsoft:

**API прейскуранта соглашения Microsoft Customer (асинхронный интерфейс API REST)**

Этот API предназначен для соглашений клиентов Microsoft, а также дополнительные атрибуты.

**Прайс-листа для области профиля выставления счетов в учетной записи выставления счетов**

Этот API является существующий интерфейс API. Он был обновлен для предоставления прайс-листа для профиля выставления счетов в учетной записи выставления счетов.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Прайс-листа для области путем платежный счет

Проверка подлинности Azure Resource Manager используется в том случае, если вы получаете прайс-листа в области действия регистрации в учетной записи выставления счетов.

Чтобы получить прайс-листа в учетной записи регистрации в учетной записи выставления счетов:

| Метод | URI запроса |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Соглашения Майкрософт клиента используйте сведения в следующем разделе. Он предоставляет свойства поля, используемые для соглашений клиентов Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Прайс-листа для области профиля выставления счетов в учетной записи выставления счетов

Обновленные прайс-лист по платежный счет API возвращает прайс-листа в формате CSV. Чтобы получить прайс-листа в области профиля выставления счетов для MCA:

| Метод | URI запроса |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

В области регистрации EA ответ API и свойства идентичны. Свойства соответствуют те же свойства MCA.

Более старые свойства [API-интерфейсов Azure Resource Manager цена лист](/rest/api/consumption/pricesheet) и те же свойства нового находятся в следующей таблице.

| Свойства API старого Azure Resource Manager Цена таблицы стилей  | Новое свойство API Microsoft Customer соглашения прейскуранта   | Описание |
| --- | --- | --- |
| Идентификатор средства измерения | _meterId_ | Уникальный идентификатор для единицы измерения. Совпадает с идентификатором meterId. |
| Название измерения | meterName | Имя индикатора. Выражают развертываемый ресурс службы Azure. |
| Категория измерения  | служба | Имя категории, классификации для единицы измерения. То же, как служба в прайс-лист соглашения Майкрософт клиента. Точная строка значения отличаются. |
| Подкатегория измерения | meterSubCategory | Имя категории подклассификация индикатора. На основе классификации дифференциации набор основных функций в службе. Например основные SQL DB vs базы данных SQL Standard. |
| Регион измерения | meterRegion | &nbsp;  |
| Единица | _неприменимо_ | Может быть проанализировано из unitOfMeasure. |
| Единица измерения | unitOfMeasure | &nbsp;  |
| Артикул | _неприменимо_ | Вместо partnumber: используйте productOrderName и снабжены для однозначной идентификации цена за профиля выставления счетов. Поля перечислены в счете MCA вместо partnumber: в MCA счета. |
| Цена за единицу | unitPrice | Цена за единицу соглашении клиента Microsoft. |
| Код валюты | pricingCurrency | Соглашений клиентов Microsoft представляют цены в цены валюты и выставление счетов валюты. Код валюты совпадает со значением pricingCurrency в соглашениях Microsoft Customer. |
| Включенное количество | includedQuantity | Не применимо к службам в соглашениях Microsoft Customer. Показать с нулевыми значениями. |
|  Идентификатор предложения  | productOrderName | Вместо OfferId используйте productOrderName. Который отличается от OfferId, тем не менее productOrderName и индикатор определения цены в соглашениях Microsoft Customer. Связанные с идентификатором meterId и Offerid в устаревших регистраций. |

Цена за соглашений клиентов Microsoft определяется иначе, чем соглашения Enterprise. Цены для служб в соглашение о регистрации Enterprise является уникальным для продукта, partnumber:, индикатор и предложения. Partnumber: не используется в соглашениях Microsoft Customer.

Цена службы потребления Azure, который является частью соглашение клиента Microsoft является уникальным для productOrderName и снабжены. Они представляют индикаторе службы и плана продукта.

Чтобы согласовать между прайс-листа, а также использования в API сведений об использовании, можно использовать productOrderName и снабжены.

Пользователи, имеющие выставления счетов профиля владелец, участник, читатель и выставлять счета manager rights можно загрузить прайс-листа.

Прайс-лист включает цены на службы, цена которых основана на использовании. Службы включают потребление ресурсов Azure и потребление Marketplace. Последними ценами в конце каждого периода службы заблокировано и применяется для использования в течение одной службы. Для служб Azure потребления период обслуживания не обычно календарного месяца.

### <a name="retired-price-sheet-api-fields"></a>Удалено API прейскуранта поля

Следующие поля являются либо не доступны в API-интерфейсов Microsoft Customer соглашения цена лист или имеют те же поля.

|Удалено поле| Описание|
|---|---|
| billingPeriodId | Не применимо. Соответствует InvoiceId для MCA. |
| offerId | Не применяется Соответствует productOrderName в MCA. |
| meterCategory  | Не применяется Соответствует службе в MCA. |
| единица измерения | Не применяется Может быть проанализировано из unitOfMeasure. |
| currencyCode | Совпадает со значением pricingCurrency в MCA. |
| meterLocation | Совпадает со значением meterRegion в MCA. |
| partnumber: partnumber: | Неприменимо, поскольку в счета-фактуры MCA отсутствует серийный номер. Вместо partnumber: используйте сочетание meterId и productOrderName для однозначной идентификации цены. |
| totalIncludedQuantity | Не применяется |
| pretaxStandardRate  | Не применяется |

## <a name="reservation-instance-charge-api-replaced"></a>Интерфейс API платежей резервирования экземпляр заменен

Вы можете получить выставления счетов транзакций для покупки резервирования с [интерфейс API платежей в зарезервированный экземпляр](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Новый API включает все покупки, в том числе сторонние предложения Marketplace. Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request). Зарезервированные интерфейс API платежей в экземпляр заменяется транзакций API.

Чтобы получить резервирования операций покупки с помощью API транзакций:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Рекомендации по API-интерфейсов замены

API-интерфейсы рекомендации покупки зарезервированного экземпляра предоставляют об использовании виртуальных машин за последние 7, 30 или 60 дней. API также предоставляют рекомендации покупки резервирования. К ним относятся следующие:

- [Общий API рекомендаций зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Единый API рекомендаций зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request). Рекомендации резервирования, API-интерфейсы перечисленных выше заменяются [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Чтобы получить рекомендации резервирования с помощью API рекомендаций резервирования:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Замена API-интерфейсов использования резервирования

Можно получить данные об использовании резервирования в регистрации с помощью API использования зарезервированного экземпляра. Есть ли более чем одним зарезервированным экземпляром в запись регистрации, можно также получить использование все покупки зарезервированного экземпляра с помощью этого API.

К ним относятся следующие:

- [Сведения об использовании зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Сводные данные об использовании зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Все интерфейсы API потребления заменяются машинных интерфейсов API Azure, используйте Azure AD для аутентификации и авторизации. Дополнительные сведения о вызывать интерфейсы API REST Azure, см. в разделе [Приступая к работе с REST](/rest/api/azure/#create-the-request). Рекомендации резервирования, API-интерфейсы перечисленных выше заменяются [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) и [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API-интерфейсы.

Чтобы получить сведения о резервировании с помощью API сведения о резервировании:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Для получения сводки резервирования с помощью API сводок резервирования:

| Метод | URI запроса |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Перемещение из Cloudyn к управлению затратами

Организации, использующие [Cloudyn](https://cloudyn.com) должны начать использовать [управление затратами Azure](https://azure.microsoft.com/services/cost-management/) для любой потребности управления затрат. Управления затратами предоставляется на портале Azure с помощью нет адаптации и 8 часовой задержки. Дополнительные сведения см. в разделе [управление затратами документации](index.yml).

В Azure Cost Management вы можете:

- Просмотр затрат со временем от заданный бюджет. Анализ ежедневно стоимость шаблонов обнаруживать и прекращать расходы аномалии. Разделите на затраты, теги, группу ресурсов, службы и расположение.
- Создание бюджета, чтобы установить ограничения на использование и расходы и получайте уведомления о важных пороговых значений. Настройка службы автоматизации с группами действий для запуска пользовательских событий и применения жестких ограничений на ваших условиях.
- Оптимизация затрат и использования с помощью рекомендаций помощника по Azure. Обнаружение оптимизации покупки с помощью резервирования, уменьшать их размер простаивающие виртуальные машины и удалите неиспользуемые ресурсы, чтобы оставаться в пределах бюджета.
- Планирование затрат и использования Экспорт данных для публикации в CSV-файл в учетной записи хранения ежедневно. Автоматизируйте интеграцию с внешними системами, чтобы сохранить данные о выставлении счетов, синхронизацию и в актуальном состоянии.

## <a name="power-bi-integration"></a>Интеграция Power BI

Если вы используете Power BI для создания отчетов затрат, вам потребуется переход на следующее.

- Microsoft Azure Consumption Insights приложение Power BI
- Соединитель Azure Consumption Insights рабочего стола


Соединитель рекомендуется для организаций, желающих наибольшую гибкость. Тем не менее в приложении Power BI доступна также для быстрого настройки.

- Установка [Microsoft Azure Consumption Insights Power BI приложения](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Подключение с помощью соединителя Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

Более старые Consumption Insights пакет содержимого и соединитель работал на уровне регистрации. Он требуется доступ на чтение. Новое приложение Power BI Insights потребления и новый соединитель Azure Consumption Insights доступны для выставления счетов профиля пользователей. Групп, которым требуется Дополнительные параметры для просмотра затрат или для просмотра затрат для выставления счетов профили следует использовать в [анализ затрат](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) портала Azure.

## <a name="next-steps"></a>Дальнейшие действия

- Чтение [управление затратами документации](index.yml) чтобы узнать, как отслеживать и контролировать издержки на Azure. Или, если вы хотите оптимизировать использование ресурсов в решении Cost Management.
