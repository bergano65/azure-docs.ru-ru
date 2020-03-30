---
title: IP-адреса службы управления API Azure (ru) Документы Майкрософт
description: Узнайте, как получить IP-адреса службы управления API Azure и когда они изменятся.
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
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047744"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-адреса управления API Azure

В этой статье мы описываем, как получить IP-адреса службы управления API Azure. IP-адреса могут быть общедоступными или закрытыми, если служба находится в виртуальной сети.

Можно использовать IP-адреса для создания правил брандмауэра, фильтрации входящего трафика в бэкэнд-сервисах или ограничения исходящего трафика.

## <a name="ip-addresses-of-api-management-service"></a>IP-адреса службы управления API

Каждый экземпляр службы управления API в уровне Developer, Basic, Standard или Premium имеет общедоступные IP-адреса, которые являются эксклюзивными только для этого экземпляра службы (они не передаются другим ресурсам). 

Можно получить IP-адреса из панели мониторинга ресурса на портале Azure.

![IP-адрес Управления API](media/api-management-howto-ip-addresses/public-ip.png)

Вы также можете получить их программно со следующим вызовом API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Публичные IP-адреса будут частью ответа:

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

В [многорегиональных развертываниях](api-management-howto-deploy-multi-region.md)каждое региональное развертывание имеет один публичный IP-адрес.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-адреса службы управления API в VNet

Если служба Управления API находится внутри виртуальной сети, она будет иметь два типа IP-адресов - публичные и частные.

Публичные IP-адреса используются `3443` для внутренней коммуникации в порту - для управления конфигурацией (например, через менеджер ресурсов Azure). Во внешней конфигурации VNet они также используются для трафика API времени выполнения. Когда запрос отправляется от Управления API в общедоступный (интернет-лицом) бэкэнд, общедоступный IP-адрес будет виден как источник запроса.

Частные виртуальные IP -1 (VIP) адреса, доступные **только** во [внутреннем режиме VNet,](api-management-using-with-internal-vnet.md)используются для подключения из сети к конечным точкам управления API - шлюзам, порталу разработчиков и плоскости управления для прямого доступа API. Вы можете использовать их для настройки Записей DNS в сети.

Адреса обоих типов будут на портале Azure и в ответе на вызов API:

![Управление API в IP-адресе VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


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

API Management использует общедоступный IP-адрес для подключения за пределами VNet и частный IP-адрес для подключения в VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-адреса службы управления API уровня потребления

Если служба управления API является службой уровня потребления, у нее нет специального IP-адреса. Служба уровня потребления работает на общей инфраструктуре и без детерминированного IP-адреса. 

В целях ограничения трафика можно использовать диапазон IP-адресов центров обработки данных Azure. Для получения точных шагов обратитесь к [статье документации Azure Functions.](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)

## <a name="changes-to-the-ip-addresses"></a>Изменения в IP-адресах

В уровнях управления API-разработчиком, базовым, стандартным и премиум-классами public IP-адреса (VIP) являются статичными для всего срока службы, за следующими исключениями:

* Служба удалена или создана повторно.
* Подписка на службу переведена в состояние [Приостановлено](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) или [С предупреждением](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (например, из-за неуплаты), а затем ее действие восстановлено.
* Виртуальная сеть Azure добавляется или удаляется из службы.
* Служба управления API переключается между внешним и внутренним режимом развертывания VNet.

В [многорегиональных развертываниях](api-management-howto-deploy-multi-region.md)региональный IP-адрес изменяется, если регион освобождается, а затем восстанавливается.
