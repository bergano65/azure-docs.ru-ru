---
title: IP-адреса службы управления API Azure | Документация Майкрософт
description: Узнайте, как получить IP-адреса службы управления API Azure и при их изменении.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 6e12a16b221a8f31d82a002fbc93b090d6f0c577
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861253"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-адреса службы управления API Azure

В этой статье описывается, как получить IP-адреса службы управления API Azure. IP-адреса могут быть общедоступными или частными, если служба находится в виртуальной сети.

Можно использовать IP-адреса для создания правил брандмауэра, фильтрации входящего трафика во внутренние службы или ограничения исходящего трафика.

## <a name="ip-addresses-of-api-management-service"></a>IP-адреса службы управления API

Каждый экземпляр службы управления API на уровне Developer, Basic, Standard или Premium имеет общедоступные IP-адреса, которые являются эксклюзивными только для этого экземпляра службы (они не используются совместно с другими ресурсами). 

IP-адреса можно получить на панели мониторинга обзора ресурса в портал Azure.

![IP-адрес управления API](media/api-management-howto-ip-addresses/public-ip.png)

Их также можно получить программно с помощью следующего вызова API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Общедоступные IP-адреса будут частью ответа:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

При [развертывании в нескольких регионах](api-management-howto-deploy-multi-region.md)каждое из региональных развертываний имеет один общедоступный IP-адрес.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-адреса службы управления API в виртуальной сети

Если служба управления API находится внутри виртуальной сети, она будет иметь два типа IP-адресов — общедоступные и частные.

Общедоступные IP-адреса используются для внутреннего взаимодействия `3443` через порт — для управления конфигурацией (например, с помощью Azure Resource Manager). Кроме того, когда запрос отправляется из службы управления API в общедоступную серверную часть (с выходом в Интернет), общедоступный IP-адрес будет отображаться как источник запроса.

Частные виртуальные IP-адреса (VIP) используются для подключения из сети к конечным точкам управления API — шлюзам, порталу разработчика и плоскости управления для прямого доступа через API. Их можно использовать для настройки записей DNS в сети.

Вы увидите адреса обоих типов в портал Azure и в ответе на вызов API:

![Управление API в IP-адресе виртуальной сети](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-адреса службы управления API уровня потребления

Если служба управления API является службой уровня потребления, она не имеет выделенного IP-адреса. Служба уровня потребления работает в общей инфраструктуре и без детерминированного IP-адреса. 

В целях ограничения трафика можно использовать диапазон IP-адресов центров обработки данных Azure. Точные инструкции см. в [статье документации по функциям Azure](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Изменения IP-адресов

На уровнях Developer, Basic, Standard и Premium интерфейса управления API общедоступные IP-адреса (VIP) статичны во время существования службы, за исключением следующих:

* Служба удалена или создана повторно.
* Подписка на службу переведена в состояние [Приостановлено](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) или [С предупреждением](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (например, из-за неуплаты), а затем ее действие восстановлено.
* Виртуальная сеть Azure добавляется в службу или удаляется из нее.
* Служба управления API переключена между внешним и внутренним режимами развертывания виртуальной сети.

При [развертывании в нескольких регионах](api-management-howto-deploy-multi-region.md)региональные IP-адреса изменяются, если регион освобожден, а затем возобновляется.
