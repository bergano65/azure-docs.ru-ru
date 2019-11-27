---
title: Переход с Соглашение Enterprise на API-интерфейсы соглашения Майкрософт для клиентов в Azure | Документация Майкрософт
description: Эта статья поможет вам понять последствия миграции Соглашение Enterprise Майкрософт (EA) в соглашение клиента Майкрософт.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 20d83c48fb4ad60b091dc87b224a053690251a48
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481722"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Переход с Соглашение Enterprise на API-интерфейсы пользовательского соглашения Майкрософт

Эта статья поможет вам понять структуру данных, API и другие различия в системной интеграции между учетными записями Соглашение Enterprise (EA) и Microsoft Customer Agreement (MCA). Служба управления затратами Azure поддерживает интерфейсы API для обоих типов учетных записей. Прежде чем продолжить, ознакомьтесь со статьей [Настройка учетной записи выставления счетов для](../billing/mca-setup-account.md) клиента Майкрософт.

Организации с имеющейся учетной записью EA должны ознакомиться с этой статьей вместе с настройкой учетной записи MCA. Ранее продление учетной записи EA требовало некоторой минимальной работы для перехода от старой регистрации к новой. Однако переход на учетную запись MCA требует дополнительных усилий. Дополнительные усилия обусловлены изменениями в базовой подсистеме выставления счетов, которые влияют на все API-интерфейсы, связанные с затратами, и предложения услуг.

## <a name="mca-apis-and-integration"></a>API-интерфейсы MCA и интеграция

Интерфейсы API MCA и новая интеграция позволяют выполнять следующие задачи:

- Полная доступность API через собственные API-интерфейсы Azure.
- Настройте несколько счетов в одной учетной записи выставления счетов.
- Доступ к комбинированному API с использованием служб Azure, использования решений сторонних разработчиков и покупок в Marketplace.
- Просмотр затрат по профилям выставления счетов (аналогично регистрациям) с помощью службы "Управление затратами Azure".
- Доступ к новым API-интерфейсам для отображения затрат, получения уведомлений о превышении заранее определенных пороговых значений и автоматического экспорта необработанных данных.

## <a name="migration-checklist"></a>Контрольный список миграции

Следующие элементы помогают переходить на API-интерфейсы MCA.

- Ознакомьтесь с новой [учетной записью для выставления счетов в рамках соглашения с клиентами Майкрософт](../billing/billing-mca-overview.md).
- Определите, какие интерфейсы API вы используете, и посмотрите, какие из них заменяются в следующем разделе.
- Ознакомьтесь с [Azure Resource Manager интерфейсами API-интерфейса RESTful](/rest/api/azure).
- Если вы еще не используете Azure Resource Manager API, [зарегистрируйте клиентское приложение в Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Обновите код программирования для [использования проверки подлинности Azure AD](/rest/api/azure/#create-the-request).
- Обновите код программирования для замены вызовов API EA с помощью вызовов API MCA.
- Обновите обработку ошибок, чтобы использовать новые коды ошибок.
- Ознакомьтесь с дополнительными предложениями по интеграции, такими как Cloudyn и Power BI, для других необходимых действий.

## <a name="ea-apis-replaced-with-mca-apis"></a>API-интерфейсы EA заменены API MCA

API EA используют ключ API для проверки подлинности и авторизации. API-интерфейсы MCA используют проверку подлинности Azure AD.

| Назначение | API EA | API MCA |
| --- | --- | --- |
| Баланс и кредиты | [/баланцесуммари](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Аваилаблебаланцеуссае |
| Использование (JSON) | [/усажедетаилс](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/усажедетаилсбикустомдате](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft. потребление/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Использование (CSV) | [/усажедетаилс/Довнлоад](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/усажедетаилс/субмит](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. потребление/usageDetails/Загрузка](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Использование Marketplace (CSV) | [/маркетплацечаржес](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/маркетплацечаржесбикустомдате](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. потребление/usageDetails/Загрузка](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Расчетный период | [/биллингпериодс](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/счета |
| прейскурант; | [/прицешит](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. Bill/Биллингаккаунтс/Биллингпрофилес/прайс-лист/Default/скачать формат = JSON|CSV Microsoft. Billing/Биллингаккаунтс/.../Биллингпрофилес/.../счета/... формат/прицешит/дефаулт/Довнлоад = JSON|CSV Microsoft. Billing/Биллингаккаунтс/.. /биллингпрофилес/.. /провидерс/микрософт.консумптион/прицешитс/довнлоад  |
| Покупки резервирований | [/ресерватиончаржес](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Transactions |
| Рекомендации по резервированию | [/Шаредресерватионрекоммендатионс](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [синглересерватионрекоммендатионс](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. потребление/Ресерватионрекоммендатионс](/rest/api/consumption/reservationrecommendations/list) |
| Использование резервирования | [/ресерватиондетаилс](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/ресерватионсуммариес](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. потребление/ресерватиондетаилс](/rest/api/consumption/reservationsdetails)[Microsoft. потребление/ресерватионсуммариес](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> служба Azure и использование решений сторонних производителей доступны в [API сведений об использовании](/rest/api/consumption/usagedetails).

Для выставления счетов MCA доступны следующие API:

| Назначение | API Microsoft Customer Agreement (MCA) |
| --- | --- |
| Счета выставления счетов<sup>2</sup> | Microsoft. Billing/Биллингаккаунтс |
| Профили выставления счетов<sup>2</sup> | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес |
| Счета в разделах<sup>2</sup> | Microsoft. Billing/Биллингаккаунтс/Инвоицесектионс |
| Счета | Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/счета |
| Подписки на выставление счетов | {Scope}/Биллингсубскриптионс |

<sup>2</sup> API возвращают списки объектов, которые являются областями, в которых работают функции управления затратами в портал Azure и API. Дополнительные сведения об областях управления затратами см. [в разделе понимание и работа с областями](understand-work-scopes.md).

При использовании существующих API-интерфейсов EA необходимо обновить их для поддержки счетов выставления счетов MCA. В следующей таблице показаны другие изменения интеграции.

| Назначение | Старое предложение | Новое предложение |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Пакет содержимого и соединитель для [аналитики потребления (Майкрософт](/power-bi/desktop-connect-azure-consumption-insights) ) | [Microsoft Azure Consumption Insights Power BI приложение](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) и [соединитель Azure consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Интерфейсы API для получения баланса и кредитов

Сводный API [получения баланса](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) предоставляет ежемесячную сводку по следующим параметрам:

- сведения о балансе.
- Новые покупки
- Плата за службу Azure Marketplace
- Корректировка
- Плата за избыточность услуг

Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request).

API сводки получения баланса заменяется API Microsoft. Billing/Биллингаккаунтс/Биллингпрофилес/Аваилаблебаланце.

Чтобы получить доступные балансы с помощью API доступного баланса:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API-интерфейсы для получения затрат и использования

Получите ежедневное разделение затрат на использование службы Azure, использование сторонних решений Marketplace и другие покупки в Marketplace с помощью следующих API-интерфейсов. Следующие отдельные API были объединены для служб Azure и использования решений сторонних разработчиков. Старые API-интерфейсы заменяются API [Microsoft. потреблению/usageDetails](/rest/api/consumption/usagedetails) . Он добавляет покупки Marketplace, которые ранее отображались только в сводке баланса до текущей даты.

- [Получение сведений об использовании и загрузка](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Получение сведений об использовании и отправка](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Получение сведений об использовании/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Получение сведений об использовании/усажедетаилсбикустомдате](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Получите магазин магазина Marketplace или маркетплацечаржес](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Получите магазин магазина Marketplace или маркетплацечаржесбикустомдате](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request).

Все предыдущие API-интерфейсы заменяются API сведений о потреблении и использовании.

Чтобы получить сведения об использовании в API сведений об использовании, выполните следующие действия.

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

API сведений об использовании, как и для всех API-интерфейсов управления затратами, доступен в нескольких областях. Для оплаты по счетам, как вы традиционно получаете на уровне регистрации, используйте область профиля выставления счетов.  Дополнительные сведения об областях управления затратами см. [в разделе понимание и работа с областями](understand-work-scopes.md).

| введите | Формат идентификатора |
| --- | --- |
| учетная запись выставления счетов; | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Профиль выставления счетов | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| подписку | `/subscriptions/{subscriptionId}` |
| Группа ресурсов | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Используйте следующие параметры строки запроса для обновления кода программирования.

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

Имя свойства, содержащего массив записей об использовании, изменилось с данных на _значения_. Каждая запись используется для получения неструктурированного списка подробных свойств. Однако каждая запись теперь находится во вложенном свойстве с именем _Properties_, за исключением тегов. Новая структура согласуется с другими API-интерфейсами Azure. Некоторые имена свойств изменились. В следующей таблице показаны соответствующие свойства.

| Старое свойство | Создать свойство | Примечания |
| --- | --- | --- |
| AccountId | Недоступно | Создатель подписки не будет отслеживанием. Используйте Инвоицесектионид (то же, что и departmentId). |
| Аккаунтнамеаккаунтовнерид и Аккаунтовнеремаил | Недоступно | Создатель подписки не будет отслеживанием. Используйте Инвоицесектионнаме (то же, что и Департментнаме). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Обратите внимание, что эти свойства являются противоположными. Если Исазурекредитенаблед имеет значение true, Чаржесбилледсепаратели будет иметь значение false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Точные строковые значения могут отличаться. |
| консумедсервицеид | Нет | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Дата и Усажестартдате | дата | &nbsp;  |
| День | Нет | Анализирует дату начала дня. |
| DepartmentId | invoiceSectionId | Точные значения различаются. |
| DepartmentName | invoiceSectionName | Точные строковые значения могут отличаться. При необходимости настройте разделы счета для соответствия подразделениям. |
| Расширенные затраты и стоимость | костинбиллингкурренци | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Является повторяющимся | Нет | &nbsp;  |
| Место проведения | location | &nbsp;  |
| MeterCategory | meterCategory | Точные строковые значения могут отличаться. |
| Значение MeterId | meterId | Точные строковые значения различаются. |
| MeterName | meterName | Точные строковые значения могут отличаться. |
| MeterRegion | meterRegion | Точные строковые значения могут отличаться. |
| meterSubCategory | meterSubCategory | Точные строковые значения могут отличаться. |
| Месяц | Нет | Анализирует дату начала месяца. |
| Название предложения | Нет | Используйте publisherName и Продуктордернаме. |
| OfferId | Нет | &nbsp;  |
| Номер заказа | Нет | &nbsp;  |
| партнумбер | Нет | Используйте meterId и Продуктордернаме для уникальной идентификации цен. |
| Имя плана | productOrderName | &nbsp;  |
| product | product |   |
| ProductId | productId | Точные строковые значения различаются. |
| Имя издателя | publisherName | &nbsp;  |
| ResourceGroup | имя_группы_ресурсов | &nbsp;  |
| ResourceGuid | meterId | Точные строковые значения различаются. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ресаурцелокатионид | Нет | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Недоступно | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Точные строковые значения могут отличаться. |
| сервицетиер | meterSubCategory | Точные строковые значения могут отличаться. |
| StoreServiceIdentifier | Недоступно | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Параметр SubscriptionName | subscriptionName | &nbsp;  |
| Теги | Теги | Свойство Tags применяется к корневому объекту, а не к свойству вложенных свойств. |
| UnitOfMeasure | unitOfMeasure | Точные строковые значения различаются. |
| usageEndDate | дата | &nbsp;  |
| Год | Нет | Синтаксический анализ года с даты. |
| новую | billingCurrency | Валюта, используемая для оплаты. |
| новую | billingProfileId | Уникальный идентификатор для профиля выставления счетов (то же, что и для регистрации). |
| новую | billingProfileName | Имя профиля выставления счетов (то же, что и регистрация). |
| новую | ChargeType | Используйте для различения использования службы Azure, использования Marketplace и покупок. |
| новую | invoiceId | Уникальный идентификатор счета. Пусто для текущего открытого месяца. |
| новую | publisherType | Тип издателя для покупок. Пусто для использования. |
| новую | serviceFamily | Тип покупки. Пусто для использования. |
| новую | servicePeriodEndDate | Дата окончания для приобретенной службы. |
| новую | servicePeriodStartDate | Дата начала для приобретенной службы. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API периодов выставления счетов заменен API-интерфейсом счетов

В счетах для выставления счетов MCA не используются периоды выставления счетов. Вместо этого они используют счета, чтобы ограничить расходы на определенные периоды выставления счетов. [API периодов выставления счетов](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) заменяется API-интерфейсом счетов. Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request).

Чтобы получить счета с помощью API счетов:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API-интерфейсы прайс-листа

В этом разделе обсуждаются существующие API-интерфейсы прайс-листов и предоставляются рекомендации по переходу на API прайс-листа для клиентских соглашений Майкрософт. В нем также обсуждается API прайс-листа для соглашений клиента Майкрософт и объясняются поля в прайс-листах. API-интерфейсы " [Корпоративный](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) доступ" и " [получить периоды выставления счетов](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) за предприятие" заменены API прайс-листа для соглашений клиента Майкрософт (Microsoft. Billing/биллингаккаунтс/биллингпрофилес/прайс-лист). Новый API поддерживает форматы JSON и CSV в асинхронных форматах RESTFUL. Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Корпоративные API выставления счетов

Вы использовали корпоративные API выставления счетов с регистрацией предприятия для получения сведений о ценах и расчетном периоде. Проверка подлинности и авторизация, используемые Azure Active Directory веб-маркеров.

Чтобы получить применимые цены для указанной корпоративной регистрации с помощью API прайс-листа и расчетного периода, выполните следующие действия.

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| ПОЛУЧЕНИЕ | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API прайс-листа для соглашений клиента Майкрософт

Используйте API прайс-листа для соглашений клиента Майкрософт, чтобы просмотреть цены для всех служб потребления Azure и Marketplace. Цены, указанные для профиля выставления счетов, применяются ко всем подпискам, принадлежащим к профилю выставления счетов.

Используйте API прайс-листа для просмотра всех данных прайс-листа служб потребления Azure в формате CSV:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Используйте API прайс-листа для просмотра всех данных прайс-листа служб потребления Azure в формате JSON:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Использование API возвращает прайс-лист для всей учетной записи. Однако можно также получить сокращенную версию прайс-листа в формате PDF. Сводка включает в себя использование Azure и услуги по потреблению рынка, которые выставляются за конкретный счет. Счет определяется с помощью {Инвоицеид}, который совпадает с **номером счета** , отображаемым в PDF-файлах сводки по счетам. Ниже приведен пример.

![Пример изображения, в котором показан номер счета, соответствующий Инвоицеид](./media/migrate-cost-management-api/invoicesummary.png)

Для просмотра сведений о счете с помощью API прайс-листа в формате CSV:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Для просмотра сведений о счете с помощью API прайс-листа в формате JSON:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Вы также можете просмотреть оценочные цены за использование Azure или службы "потребление в Marketplace" в текущем открытом цикле выставления счетов или сервисном периоде.

Для просмотра оценочных цен на услуги потребления с помощью API прайс-листа в формате CSV:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Для просмотра оценочных цен на услуги потребления с помощью API прайс-листа в формате JSON:

| Метод | URI запроса |
| --- | --- |
| ПУБЛИКАЦИЯ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

API-интерфейсы прайс-листа соглашения для клиентов Майкрософт — это *асинхронные интерфейсы API RESTful*. Ответы для API-интерфейсов изменились со старых синхронных API. Тело ответа API также изменилось.

#### <a name="old-response-body"></a>Старый текст ответа

Ниже приведен пример синхронного REST API ответа.

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

API-интерфейсы поддерживают [асинхронный формат Azure RESTful](../azure-resource-manager/resource-manager-async-operations.md) . Вызовите API с помощью GET, и вы получите следующий ответ:

```
No Response Body

HTTP Status 202 Accepted
```

Следующие заголовки отправляются с расположением выходных данных:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Выполните еще один вызов GET в расположение. Ответ на вызов GET остается тем же, пока операция не достигнет состояния завершения или сбоя. По завершении ответ на расположение вызова GET возвращает URL-адрес загрузки. Точно так же, как если бы операция выполнялась в то же время. Ниже приведен пример:

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

В следующей таблице показаны поля в API-интерфейсе "Корпоративный получение прайс-листа". Он включает соответствующие поля в новом прайс – листе для клиентских соглашений Майкрософт:

| Старое свойство | Создать свойство | Примечания |
| --- | --- | --- |
| billingPeriodId  | _Неприменимо_ | Не применяется Для соглашений клиента Майкрософт счет и связанный прайс/лист заменены понятием Биллингпериодид. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Точные строковые значения могут отличаться. |
| includedQuantity  | includedQuantity | Неприменимо для служб в соглашениях клиента Майкрософт. |
| partNumber  | _Неприменимо_ | Вместо этого используйте сочетание Продуктордернаме (то же, что и offerId) и meterid. |
| unitPrice  | unitPrice | Цена за единицу применима для служб, потребляемых в соглашениях клиентов Майкрософт. |
| currencyCode  | приЦингкурренци | В соглашениях клиента Майкрософт цены представлены в ценовой валюте и валюте выставления счетов. Курренцикоде соответствует ПриЦингкурренци в соглашениях клиентов Майкрософт. |
| offerId | productOrderName | Вместо OfferId можно использовать Продуктордернаме, но не то же, что OfferId. Однако Продуктордернаме и измерительные отношения определяют цены в соглашениях клиентов Майкрософт, связанных с meterId и OfferId в устаревших регистрациях. |

## <a name="consumption-price-sheet-api-operations"></a>Операции API прайс-листа потребления

Для соглашений Enterprise вы [использовали API прайс](/rest/api/consumption/pricesheet/get) -листа потребления и получать по операциям [расчетного периода](/rest/api/consumption/pricesheet/getbybillingperiod) для области по SubscriptionId или расчетному периоду. API использует проверку подлинности управления ресурсами Azure.

Чтобы получить сведения о прайс-листе для области с помощью API прайс-листа:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Чтобы получить сведения о прайс-листе по расчетному периоду с помощью API прайс-листа:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Вместо указанных выше конечных точек API используйте следующие функции для клиентских соглашений Майкрософт:

**API прайс-листа для соглашений клиента Майкрософт (асинхронный REST API)**

Этот API предназначен для клиентских соглашений Майкрософт и предоставляет дополнительные атрибуты.

**Прайс-лист для области профиля выставления счетов в счете**

Этот API является существующим API. Он был обновлен для предоставления прайс-листа для профиля выставления счетов в счете.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Прайс-лист для области по счету выставления счетов

Azure Resource Manager проверка подлинности используется при получении прайс-листа в области регистрации в учетной записи выставления счетов.

Чтобы получить прайс-лист в учетной записи регистрации в учетной записи выставления счетов, выполните следующие действия.

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Для соглашения клиента Майкрософт используйте сведения, приведенные в следующем разделе. Он предоставляет свойства полей, используемые для клиентских соглашений Майкрософт.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Прайс-лист для области профиля выставления счетов в счете

Обновленный прайс-лист с помощью API счета выставления счетов получает прайс-лист в формате CSV. Чтобы получить прайс лист в области профиля выставления счетов для MCA, сделайте следующее:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

В области регистрации EA ответ и свойства API идентичны. Свойства соответствуют одним и тем же свойствам MCA.

Более старые свойства для [Azure Resource Manager интерфейсов API прайс-листа](/rest/api/consumption/pricesheet) и новые свойства приведены в следующей таблице.

| Свойство API для Azure Resource Manager прайс-листа старой цены  | Новое свойство API прайс-листа соглашения для клиентов Майкрософт   | ОПИСАНИЕ |
| --- | --- | --- |
| Идентификатор средства измерения | _meterId_ | Уникальный идентификатор для средства измерения. То же, что и meterId. |
| Имя счетчика | meterName | Имя индикатора. Счетчик представляет ресурс, развертываемый службой Azure. |
| Категория счетчиков  | Служба | Имя категории классификации для единицы измерения. То же, что и служба, в прайс – листе соглашения для клиентов Майкрософт. Точные строковые значения различаются. |
| Подкатегория счетчика | meterSubCategory | Имя категории подклассификации единицы измерения. На основе классификации набора функций высокого уровня, отличающегося от службы. Например, Basic SQL DB и стандартная база данных SQL. |
| Регион счетчика | meterRegion | &nbsp;  |
| Единица измерения | _Неприменимо_ | Можно проанализировать из Унитофмеасуре. |
| Единица измерения | unitOfMeasure | &nbsp;  |
| Номер детали | _Неприменимо_ | Вместо Партнумбер используйте Продуктордернаме и MeterId для уникальной идентификации цены для профиля выставления счетов. Поля перечислены в счете MCA вместо Партнумбер в счетах MCA. |
| Цена за единицу | unitPrice | Цена за единицу соглашения клиента Майкрософт. |
| Код валюты | приЦингкурренци | Соглашения клиента Майкрософт представляют цены в валюте ценообразования и валюте выставления счетов. Код валюты совпадает с кодом ПриЦингкурренци в соглашениях клиента Майкрософт. |
| Включенное количество | includedQuantity | Неприменимо к службам в соглашениях клиента Майкрософт. Показывать с нулевыми значениями. |
|  Идентификатор предложения  | productOrderName | Вместо OfferId используйте Продуктордернаме. Не то же, что и OfferId, однако Продуктордернаме и измеритель определяют цены в соглашениях клиентов Майкрософт. Относится к meterId и OfferId в устаревших регистрациях. |

Цена для соглашений клиента Майкрософт определена не так, как соглашения Enterprise. Цена за услуги в рамках регистрации на предприятии уникальна для продукта, Партнумбер, измерительного обслуживания и предложения. Партнумбер не используется в соглашениях клиентов Майкрософт.

Цена службы потребления Azure, которая является частью соглашения клиента Майкрософт, уникальна для Продуктордернаме и meterId. Они представляют собой счетчик обслуживания и план продукта.

Чтобы согласовать прайс-лист и использование в API сведений об использовании, можно использовать Продуктордернаме и meterId.

Пользователи, имеющие права владельца профиля выставления счетов, участника, читателя и накладных, могут скачать прайс лист.

Прайс-лист содержит цены на услуги, цена которых основана на использовании. Эти службы включают использование Azure и использование Marketplace. Последняя цена в конце каждого сервисного периода блокируется и применяется к использованию в течение одного периода обслуживания. Для служб потребления Azure период обслуживания обычно является календарным месяцем.

### <a name="retired-price-sheet-api-fields"></a>Отмененные поля API прайс-листа

Следующие поля недоступны в API-интерфейсах прайс-листа соглашения для клиентов Майкрософт или имеют одинаковые поля.

|Отмененное поле| ОПИСАНИЕ|
|---|---|
| billingPeriodId | Не применимо. Соответствует Инвоицеид для MCA. |
| offerId | Не применяется Соответствует Продуктордернаме в MCA. |
| meterCategory  | Не применяется Соответствует службе в MCA. |
| unit | Не применяется Можно проанализировать из Унитофмеасуре. |
| currencyCode | То же, что и ПриЦингкурренци в MCA. |
| метерлокатион | То же, что и Метеррегион в MCA. |
| Партнумбер партнумбер | Не применяется, так как номер детали не указан в счетах MCA. Вместо партнумбер используйте сочетание meterId и Продуктордернаме для уникальной идентификации цен. |
| тоталинклудедкуантити | Не применяется |
| претаксстандардрате  | Не применяется |

## <a name="reservation-instance-charge-api-replaced"></a>Заменяется загрузкой API для экземпляра резервирования

Вы можете получить проводки счетов для покупок резервирования с помощью [API зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Новый API включает все покупки, включая сторонние предложения Marketplace. Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request). API зарезервированных экземпляров заменяется API транзакций.

Для получения зарезервированных транзакций покупки с помощью API транзакций:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Заменены API рекомендаций

Зарезервированные API рекомендации по приобретению экземпляра обеспечивают использование виртуальной машины за последние 7, 30 или 60 дней. Интерфейсы API также предоставляют рекомендации по приобретению резервирования. К ним относятся следующие:

- [API рекомендаций общего зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API рекомендаций для одного зарезервированного экземпляра](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request). Приведенные ранее API рекомендаций по резервированию заменяются API [Microsoft. потреблению/ресерватионрекоммендатионс](/rest/api/consumption/reservationrecommendations/list) .

Чтобы получить рекомендации по резервированию с помощью API рекомендаций по резервированию:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Заменяются API-интерфейсы использования резервирования

Вы можете получить сведения об использовании резервирования в регистрации с помощью Использование зарезервированных экземпляров API. Если в регистрации имеется несколько зарезервированных экземпляров, можно также получить сведения об использовании всех зарезервированных экземпляров покупки с помощью этого API.

К ним относятся следующие:

- [Сведения о Использование зарезервированных экземпляров](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Сводка Использование зарезервированных экземпляров](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Все API-интерфейсы использования заменяются собственными интерфейсами API Azure, которые используют Azure AD для проверки подлинности и авторизации. Дополнительные сведения о вызове интерфейсов API службы Azure для интерфейса Интернета см. в статье [Приступая к работе с другими компонентами](/rest/api/azure/#create-the-request). Ранее указанные API рекомендаций по резервированию заменяются интерфейсами API [Microsoft. потребляемой ресерватиондетаилс](/rest/api/consumption/reservationsdetails) и [Microsoft. потребляемой ресерватионсуммариес](/rest/api/consumption/reservationssummaries) .

Чтобы получить сведения о резервировании с помощью API сведений о резервировании:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Для получения сводок о резервировании с помощью API сводок резервирования:

| Метод | URI запроса |
| --- | --- |
| ПОЛУЧЕНИЕ | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Переход от Cloudyn к управлению затратами

Организации, использующие [Cloudyn](https://cloudyn.com) , должны начать использовать службу " [Управление затратами Azure](https://azure.microsoft.com/services/cost-management/) " для любых потребностей в управлении затратами. Управление затратами доступно в портал Azure без адаптации и 8-часовой задержкой. Дополнительные сведения см. в [документации по управлению затратами](index.yml).

С помощью службы "Управление затратами Azure" можно:

- Просмотр затрат с течением времени по стандартному бюджету. Анализируйте шаблоны ежедневных затрат, чтобы выявить и предотвратить аномалии расходов. Разбейте затраты на теги, группу ресурсов, службу и расположение.
- Создавайте бюджеты, чтобы задать ограничения на использование и затраты и получать уведомления при приближении важных пороговых значений. Настройте автоматизацию с помощью групп действий, чтобы активировать пользовательские события и применить жесткие ограничения к вашим условиям.
- Оптимизируйте затраты и использование с помощью рекомендаций из помощника по Azure. Изучите оптимизации покупок с помощью резервирования, уменьшать их размер неиспользуемые виртуальные машины и удалите неиспользуемые ресурсы, чтобы остаться в бюджетах.
- Запланируйте экспорт данных о затратах и использовании, чтобы ежедневно публиковать CSV-файл в учетной записи хранения. Автоматизируйте интеграцию с внешними системами, чтобы обеспечить синхронизацию и актуальность данных выставления счетов.

## <a name="power-bi-integration"></a>Интеграция с Power BI

Для отчетов о затратах также можно использовать Power BI. [Соединитель управления затратами Azure](/power-bi/desktop-connect-azure-cost-management) для Power BI Desktop можно использовать для создания мощных настраиваемых отчетов, которые помогут лучше понять затраты на Azure. Соединитель управления затратами Azure в настоящее время поддерживает клиентов с соглашением с клиентами Майкрософт или Соглашение Enterprise (EA).

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [документацией по управлению затратами](index.yml) , чтобы узнать, как отслеживать расходы на Azure и управлять ими. Или, если требуется оптимизировать использование ресурсов с помощью управления затратами.
