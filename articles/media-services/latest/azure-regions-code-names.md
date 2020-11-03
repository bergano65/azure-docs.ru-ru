---
title: Облака и регионы, в которых доступны службы мультимедиа Azure v3
description: В этой статье рассказывается об URL-адресах, используемых для конечных точек и кода для регионов.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 462cf6cf850885b31588123bac46ff99bf319ee8
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93243146"
---
# <a name="regional-code-names-and-endpoints"></a>Региональные имена и конечные точки кода

### <a name="region-code-name"></a>Кодовое имя региона

Если в команде или запросе используется параметр **Location** , необходимо указать имя кода региона в качестве значения **расположения** . Чтобы получить имя кода региона, в котором находится ваша учетная запись, а также для направления вызова, можно выполнить следующую строку в Azure CLI.

```azurecli-interactive
az account list-locations
```

Когда вы запустите показанную выше строку, вы увидите список всех регионов Azure. Перейдите к региону Azure со значением *displayName* , которое вы ищете, и используйте его *имя* для параметра **location**.

Например, для региона Azure "западная часть США 2" (показан ниже) для параметра **location** нужно использовать значение westus2.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Конечные точки  

При подключении к Службам мультимедиа из облака Azure другой страны необходимо знать следующие конечные точки.

### <a name="global-azure"></a>Глобальная среда Azure

| Служба | Конечная точка |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Аутентификация | `https://login.microsoftonline.com/` |
| Аудитория токена | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure для государственных организаций

| Служба | Конечная точка |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Аутентификация | `https://login.microsoftonline.us/` |
| Аудитория токена | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure для Германии

| Служба | Конечная точка |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Аутентификация | `https://login.microsoftonline.de/` |
| Аудитория токена | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Служба | Конечная точка |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Аутентификация | `https://login.chinacloudapi.cn/` |
| Аудитория токена |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>См. также статью

* [Регионы Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Региональные имена и конечные точки кода](azure-regions-code-names.md)
* [Географические регионы Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Дальнейшие действия

[Что такое Службы мультимедиа Azure версии 3?](media-services-overview.md)
