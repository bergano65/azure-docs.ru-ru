---
title: Экономия на SAP HANA (крупные экземпляры) благодаря резервированию Azure
description: Все, что нужно знать перед покупкой резервирования для SAP HANA (крупные экземпляры), и сведения о приобретении этой возможности.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ea1ee2311d8655ce17017c73309bc69e89f4b5f4
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599119"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Экономия на SAP HANA (крупные экземпляры) благодаря резервированию Azure

Приобретая предварительно резервирование Azure на год или три года, вы можете сократить затраты на SAP HANA (крупные экземпляры) (HLI). Скидка на резервирование применяется к подготовленному решению с номером SKU HLI, соответствующим приобретенному зарезервированному экземпляру. Эта статья поможет вам узнать все необходимое перед покупкой резервирования и понять, как приобрести эту возможность.

Приобретя резервирование, вы фиксируете использование HLI на год или три года. Покупка зарезервированной емкости HLI предполагает приобретение вычислительных ресурсов и хранилища NFS в одном пакете с номером SKU. Резервирование не включает в себя затраты на лицензирование такого программного обеспечения, как операционная система или решение SAP, а также дополнительные затраты на хранение. Скидка на резервирование автоматически применяется к подготовленному решению SAP HLI. По окончании срока резервирования к подготовленному ресурсу применяются тарифы для оплаты по мере использования.

## <a name="purchase-considerations"></a>Вопросы, связанные с приобретением

Перед приобретением зарезервированной емкости необходимо подготовить решение с номером SKU HLI. Резервирование оплачивается наперед или ежемесячными платежами. На зарезервированную емкость HLI налагаются следующие ограничения:

- Скидки на резервирование применяются только к подпискам с Соглашением Enterprise и Клиентским соглашением Майкрософт. Другие подписки не поддерживаются.
- Гибкость размера экземпляра для зарезервированной емкости HLI не поддерживается. Резервирование применяется только к SKU и региону, для которых оно приобретено.
- Самостоятельная отмена и обмен не поддерживаются.
- Зарезервированная емкость принадлежит к одной области, поэтому она применяется к одной подписке и группе ресурсов. Приобретенную емкость невозможно обновить для использования в другой подписке.
- Для зарезервированной емкости HANA нельзя использовать общую область резервирования. Область резервирования нельзя разделить, объединить или обновить.
- С помощью вызовов API для зарезервированной емкости можно приобрести только одно решение HLI за раз. Для приобретения дополнительных экземпляров воспользуйтесь дополнительными вызовами API.

Зарезервированную емкость можно приобрести на портале Azure или с помощью [REST API](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Приобретение резервирования для HANA (крупные экземпляры)

Используйте следующие сведения для приобретения резервирования HLI с помощью интерфейсов [REST API заказа на резервирование](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>Получение заказа на резервирование и цены

Сначала получите заказ на резервирование и цену для подготовленного решения с номером SKU для HANA (крупные экземпляры) с помощью API [расчета цены](/rest/api/reserved-vm-instances/reservationorder/calculate).

В следующем примере для вызовов REST API с помощью PowerShell используется [armclient](https://github.com/projectkudu/ARMClient). Запрос API заказа на резервирование и расчета цены и его текст должны выглядеть следующим образом:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Дополнительные сведения о полях данных и их описания см. в разделе [Поля резервирования HLI](#hli-reservation-fields).

Возвращенный результат будет похож на следующий пример ответа. Запишите значение, возвращенное для `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Осуществление покупки

Осуществите покупку, используя возвращенные значения `quoteId` и `reservationOrderId`, полученные при выполнении предыдущего этапа [Получение заказа на резервирование и цены](#get-the-reservation-order-and-price).

Ниже приведен пример запроса.

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Ниже приведен пример ответа. Если заказ размещен успешно, параметр `provisioningState` будет иметь значение `creating`.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Проверка успешности приобретения

Отправьте запрос GET для заказа на резервирование, чтобы просмотреть состояние заказа на покупку. Параметр `provisioningState` должен иметь значение `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Ответ должен выглядеть так, как показано в следующем примере.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>Поля резервирования HLI

Ниже приведено объяснение значения различных полей резервирования.

  **SKU** — имя номера SKU для HLI. Оно выглядит следующим образом: `SAP_HANA_On_Azure_<SKUname>`.

  **Location** (Расположение) — доступные регионы HLI. Сведения о доступных регионах см. в статье [Номера SKU для SAP HANA в Azure (крупные экземпляры)](../../virtual-machines/workloads/sap/hana-available-skus.md). Чтобы получить формат строки расположения, используйте [вызов API получения расположений](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Reserved Resource type** (Зарезервированный тип ресурса) — `SapHana`.

  **Subscription** (Подписка) — подписка, используемая для оплаты резервирования. Резервирование оплачивается с помощью метода оплаты, указанного для подписки. Нужно выбрать подписку с соглашением Enterprise (номера предложений: MS-AZR-0017P или MS-AZR-0148P) или Клиентским соглашением Майкрософт. Плата вычитается из остатка на счете предоплаты Azure (прежнее название — денежное обязательство), при его наличии, или относится к избыточным расходам.

  **Scope** (Область) — единственная область резервирования.

  **Term** (Срок) — один или три года. Обозначается как `P1Y` или `P3Y`.

  **Quantity** (Количество) — число экземпляров, приобретаемых для резервирования. За один раз можно приобрести только один экземпляр HLI. Для покупки дополнительных резервирований повторите вызов API с соответствующими полями.

## <a name="troubleshoot-errors"></a>Устранение неполадок

При покупке резервирования может появиться сообщение об ошибке, аналогичное приведенному ниже примеру. Возможная причина такой ошибки заключается в том, что экземпляр HLI не подготовлен для приобретения. Если это так, прежде чем пытаться приобрести резервирование, обратитесь в службу технической поддержки учетных записей Майкрософт с просьбой подготовить HLI.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, [как вызывать интерфейсы REST API службы Azure для работы с Postman и cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Список номеров SKU и сведения о доступных регионах см. в статье [Номера SKU для SAP HANA в Azure (крупные экземпляры)](../../virtual-machines/workloads/sap/hana-available-skus.md).