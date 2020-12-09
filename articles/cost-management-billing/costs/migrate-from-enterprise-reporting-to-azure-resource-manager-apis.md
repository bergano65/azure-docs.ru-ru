---
title: Переход с API для корпоративной отчетности на API Azure Resource Manager
description: Эта статья поможет вам понять различия между API отчетов и API Azure Resource Manager, разобраться в процедурах перехода на API Azure Resource Manager и в их новых возможностях.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355826"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Переход с API для корпоративной отчетности на API Azure Resource Manager

Эта статья поможет разработчикам, которые используют пользовательские решения на основе [API отчетов Azure для корпоративных клиентов](../manage/enterprise-api.md), перейти на новые API Azure Resource Manager для службы "Управление затратами". Поддержка субъектов-служб для новых интерфейсов API Azure Resource Manager теперь общедоступна. API Azure Resource Manager находятся в стадии активной разработки. Мы рекомендуем перейти на них со старых API отчетов Azure для корпоративных клиентов. Поддержка старых API будет прекращена. Эта статья поможет вам понять различия между API отчетов и API Azure Resource Manager, разобраться в процедурах перехода на API Azure Resource Manager и в их новых возможностях.

## <a name="api-differences"></a>Отличия между API

Ниже описаны различия между старыми API отчетов для корпоративных клиентов и новыми API Azure Resource Manager.

| **Использование** | **API для Соглашения Enterprise** | **API Azure Resource Manager** |
| --- | --- | --- |
| Аутентификация | Ключ API, подготовленный на EA Portal. | Аутентификация Azure Active Directory (Azure AD) с использованием маркеров пользователей или субъектов-служб. Вместо ключей API теперь используются субъекты-службы. |
| Области и разрешения | Все запросы находятся в области регистрации. Назначение разрешений для ключей API определяет, какие данные возвращаются: для всей регистрации, для отдела или для определенной учетной записи. Аутентификация пользователя не используется. | Пользователям или субъектам-службам назначается доступ к области регистрации, отдела или учетной записи. |
| Конечная точка URI | https://consumption.azure.com | https://management.azure.com |
| Состояние разработки | В режиме обслуживания. Планируется прекращение поддержки. | Активно разрабатываются. |
| Доступные API | Ограничены тем, что уже доступно. | Для каждого API EA доступны эквивалентные API на замену. <p> Кроме того, предоставляются дополнительные [API Управления затратами](/rest/api/cost-management/): <p> <ul><li>Бюджеты</li><li>видны узлы<li>Экспортируемые элементы</li></ul> |

## <a name="migration-checklist"></a>Контрольный список действий по миграции

- Ознакомьтесь с [REST API Azure Resource Manager](/rest/api/azure).
- Определите, какие API EA вы используете, и узнайте, на какие API Azure Resource Manager вам нужно перейти, из раздела [Сопоставление API EA с новыми API Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Настройка авторизации и аутентификации для службы в API Azure Resource Manager
  - Если вы еще не используете API Azure Resource Manager, [зарегистрируйте клиентское приложение в Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). Регистрация создает субъект-службу, с помощью которого вы будете вызывать API.
  - Назначьте этому субъекту-службе доступ к требуемым областям, как описано ниже.
  - Обновите программный код, чтобы использовать этот субъект-службу для [аутентификации Azure AD](/rest/api/azure/#create-the-request).
- Протестируйте работу с API и обновите программный код, чтобы заменить все вызовы API EA новыми вызовами API Azure Resource Manager.
- Обновите механизм обработки ошибок так, чтобы он использовал новые коды ошибок. Следует учесть, среди прочего, следующее:
  - API Azure Resource Manager имеют период ожидания 60 секунд.
  - К API Azure Resource Manager применяются ограничения скорости. При превышении этих ограничений появляются ошибки регулирования 429. Следите за тем, чтобы ваши решения не отправляли слишком много вызовов API в течение короткого периода времени.
- Ознакомьтесь с другими API Управления затратами, которые предоставляются через Azure Resource Manager, и оцените возможность их использования в будущем. Дополнительные сведения см. в разделе [об использовании дополнительных API Управления затратами](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Назначение субъекту-службе доступа к интерфейсам API Azure Resource Manager

После создания субъекта-службы для программного вызова API Azure Resource Manager нужно присвоить этому субъекту-службе соответствующие разрешения для авторизации и выполнения запросов в Azure Resource Manager. Существует две платформы разрешений, используемые для разных сценариев.

### <a name="azure-billing-hierarchy-access"></a>Доступ к иерархии выставления счетов Azure

Чтобы назначить субъекту-службе разрешения для доступа к областям отделов, учетных записей регистрации и учетных записей выставления счетов для корпоративных клиентов, используйте API [разрешений на выставление счетов](/rest/api/billing/2019-10-01-preview/billingpermissions), [определений ролей выставления счетов](/rest/api/billing/2019-10-01-preview/billingroledefinitions) и [назначений ролей выставления счетов](/rest/api/billing/2019-10-01-preview/billingroleassignments).

- API разрешений на выставление счетов позволяют определить разрешения, которые субъект-служба уже использует в определенной области, например в области учетной записи выставления счетов или отдела.
- API определений ролей выставления счетов позволяют перечислить доступные роли, которые могут быть назначены субъекту-службе.
  - В настоящее время субъектам-службам можно назначить права администратора EA только для чтения или администратора отдела только для чтения.
- API назначений ролей выставления счетов позволяют назначить роль для субъекта-службы.

В следующем примере показано, как вызвать API назначений ролей, чтобы предоставить субъекту-службе доступ к учетной записи выставления счетов. Настройку разрешений нужно выполнить только однократно, и для этого мы рекомендуем использовать [PostMan](https://postman.com).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Текст запроса

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Управление доступом на основе ролей в Azure

Поддержка субъектов-служб действует и для областей Azure, таких как группы управления, подписки и группы ресурсов. Вы можете назначить субъекту-службе разрешения для этих областей непосредственно [на портале Azure](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) или с помощью [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Сопоставление API EA с новыми API Azure Resource Manager

С помощью следующей таблицы вы можете определить, какие API Azure Resource Manager заменяют используемые в настоящее время API EA.

| **Сценарий** | **API EA** | **API Azure Resource Manager** |
| --- | --- | --- |
| Сводная информация о балансе | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Прейскурант | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – используется для согласованных цен <p> [API розничных цен](/rest/api/cost-management/retail-prices/azure-retail-prices) — используется для розничных цен |
| Сведения о зарезервированных экземплярах | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Сводка по зарезервированному экземпляру | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Рекомендации по резервированию экземпляров | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Оплата зарезервированных экземпляров | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Сведения о миграции по API

В следующих разделах показаны примеры запросов старых API и запросов новых API, которые их заменяют.

### <a name="balance-summary-api"></a>API сводной информации о балансе

Следующие URI запросов используются при вызове нового API сводной информации о балансе. В качестве billingAccountId укажите номер регистрации.

#### <a name="supported-requests"></a>Поддерживаемые запросы

[Получение для регистрации](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Изменения текста ответа

_Старый текст ответа:_

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Новый текст ответа:_

Эти же данные теперь доступны в поле `properties` нового ответа API. Некоторые имена полей могли незначительно измениться.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Прейскурант

Следующие URI запроса используются при вызове нового API прейскуранта.

#### <a name="supported-requests"></a>Поддерживаемые запросы

 Вы можете вызвать этот API, используя следующие области:

- Регистрация: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Подписка: `subscriptions/{subscriptionId}`

[_Получение за текущий период выставления счетов_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Получение за указанный период выставления счетов_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Изменения текста ответа

_Старый текст ответа:_

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
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
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Новый текст ответа:_

Старые данные теперь находятся в поле `pricesheets` нового ответа API. Также предоставляются сведения о единицах измерения.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Данные об использовании зарезервированного экземпляра

Корпорация Майкрософт не ведет активных работ над синхронными интерфейсами API сведений о резервировании. Мы рекомендуем в процессе миграции перейти к более новому шаблону асинхронного вызова API, поддерживаемому SPN. Асинхронные запросы лучше справляются с большими объемами данных и будут реже приводить к ошибкам времени ожидания.

#### <a name="supported-requests"></a>Поддерживаемые запросы

Используйте следующие URI запросов при вызове нового асинхронного API сведений о резервировании. В качестве `billingAccountId` укажите номер регистрации. Вы можете вызвать этот API, используя следующие области:

- Регистрация: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Пример запроса для создания отчета о резервировании

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Пример запроса для опроса состояния создания отчета

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Пример ответа на такой опрос

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Изменения текста ответа

Ниже приведен ответ на запрос к старому синхронному API сведений о резервировании.

_Старый текст ответа:_

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Новый текст ответа:_

Новый API создает для вас CSV-файл, который включает следующие поля.

| **Старое свойство** | **Новое свойство** | **Примечания** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Новое свойство для гибкости экземпляра. |
|  | InstanceFlexibilityRatio | Новое свойство для гибкости экземпляра. |
| instanceId | InstanceName |  |
|  | Kind | Новое свойство. Оно принимает значения `None`, `Reservation` или `IncludedQuantity`. |
| reservationId | Идентификатор резервирования |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Сводка об использовании зарезервированного экземпляра

Используйте следующие URI запросов для вызова нового API сводных данных о резервировании.

#### <a name="supported-requests"></a>Поддерживаемые запросы

 Вызывайте этот API, используя следующие области:

- Регистрация: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Получение сводных данных о резервировании за день_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Получение сводных данных о резервировании за месяц_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Изменения текста ответа

_Старый текст ответа:_

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Новый текст ответа:_

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Рекомендации по резервированию экземпляров

Используйте следующие URI запросов для вызова нового API рекомендаций по резервированию.

#### <a name="supported-requests"></a>Поддерживаемые запросы

 Вызывайте этот API, используя следующие области:

- Регистрация: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Подписка: `subscriptions/{subscriptionId}`
- Группы ресурсов: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Получение рекомендаций_](/rest/api/consumption/reservationrecommendations/list)

Через этот API доступны рекомендации как для одной области, так и для общих областей. Можно также фильтровать результаты по области, используя необязательный параметр API.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Изменения текста ответа

Рекомендации для общих областей и одной области теперь объединены в одном API.

_Старый текст ответа:_

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Новый текст ответа:_

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Оплата зарезервированных экземпляров

Используйте следующие URI запросов для вызова нового API оплаты зарезервированных экземпляров.

#### <a name="supported-requests"></a>Поддерживаемые запросы

[_Получение сведений о расходах на резервирование по диапазону дат_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Изменения текста ответа

_Старый текст ответа:_

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Новый текст ответа:_

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Использование дополнительных API Управления затратами

После перехода на API Azure Resource Manager для имеющихся сценариев создания отчетов вы можете настроить использование и других API. Эти API также доступны через Azure Resource Manager и могут быть автоматизированы с использованием аутентификации на основе субъекта-службы. Ниже приведен краткий обзор новых возможностей.

- [Бюджеты](/rest/api/consumption/budgets/createorupdate) — используйте для установки порогов для упреждающего мониторинга затрат, оповещения релевантных участников и автоматизации действий в ответ на нарушение порогов.

- [Оповещения](/rest/api/cost-management/alerts) — используйте для просмотра сведений об оповещениях, в том числе в отношении бюджета, счетов, кредитов и квот.

- [Экспорты](/rest/api/cost-management/exports) — используйте для создания расписаний регулярного экспорта данных о расходах в выбранную учетную запись хранения Azure. Это рекомендуемое решение для клиентов с большим присутствием в Azure, которые хотят анализировать данные и использовать их в собственных внутренних системах.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [REST API Azure Resource Manager](/rest/api/azure).
- Если потребуется, определите, какие API EA вы используете, и узнайте, на какие API Azure Resource Manager вам нужно перейти, из раздела [Сопоставление API EA с новыми API Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Если вы еще не используете API Azure Resource Manager, [зарегистрируйте клиентское приложение в Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Если потребуется, обновите программный код, чтобы использовать этот субъект-службу для [аутентификации Azure AD](/rest/api/azure/#create-the-request).