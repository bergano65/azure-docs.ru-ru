---
title: Проверка данных о выставлении счетов по подписке Azure с помощью REST API
description: Узнайте, как использовать интерфейсы REST API Azure для проверки сведений о выставлении счетов по подписке. Для настройки результатов вы можете использовать фильтры.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b4d6502e49fdd30a68188a1e580a1c137984c89f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132386"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Проверка данных о выставлении счетов по подписке с помощью интерфейсов REST API

Интерфейсы API отчетов Azure позволяют проверять данные о расходах на Azure и управлять ими.

С помощью фильтров вы можете настроить результаты в соответствии со своими требованиями.

Из этой статьи вы узнаете, как с помощью REST API получить сведения о выставлении счетов по подписке для указанного диапазона дат.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Создание запроса

`{subscriptionID}` — это обязательный параметр, который позволяет определить целевую подписку.

`{billingPeriod}` — это обязательный параметр, который позволяет указать текущий [период выставления счетов](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Параметры `${startDate}` и `${endDate}` являются обязательными в нашем примере, но не для конечной точки. Они позволяют указать диапазон дат в строковом формате ГГГГ-ММ-ДД (например, `'20180501'` и `'20180615'`).

Ниже приведены обязательные заголовки.

|Заголовок запроса|Описание|
|--------------------|-----------------|
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|
|*Authorization:*|Обязательный элемент. Задайте допустимый [маркер доступа](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

## <a name="response"></a>Ответ

Код состояния 200 (ОК) возвращается в случае успешного ответа, который содержит список затрат с подробными сведениями для учетной записи.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Каждый элемент в разделе **value** представляет сведения об использовании службы:

|Свойство ответа|Описание|
|----------------|----------|
|**subscriptionGuid** | Глобальный уникальный идентификатор для подписки. |
|**startDate** | Дата начала использования. |
|**endDate** | Дата окончания использования. |
|**usageQuantity** | Использованный объем. |
|**billableQuantity** | Фактический объем, за который выставлен счет. |
|**pretaxCost** | Стоимость, указанная в счете-фактуре, без учета применимых налогов. |
|**meterDetails** | Подробные сведения об использовании. |
|**nextLink**| Если этот параметр задан, в его значении указывается URL-адрес следующей страницы со сведениями. Если страница последняя, значение не указывается. |

Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа см. в разделе [сведений об использовании](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy).

Другие коды состояния означают состояния ошибки. В этих случаях объект ответа содержит описание с объяснением причины сбоя запроса.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия
- Ознакомьтесь со статьей [Обзор API-интерфейсов отчетов для корпоративных клиентов](./enterprise-api.md)
- Подробнее о [REST API выставления счетов для корпоративных клиентов](/rest/api/billing/)
- [Начало работы с Azure REST API](/rest/api/azure/)