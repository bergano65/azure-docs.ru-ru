---
title: Облака и регионы, в которых доступны Службы мультимедиа Azure версии 3 | Документация Майкрософт
description: В этой статье рассказывается об облаках Azure и регионах, в которых доступны Службы мультимедиа Azure версии 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/07/2019
ms.author: juliako
ms.openlocfilehash: a5a4205c97e4db25d5d0a92472610364d912b278
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963443"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Облака и регионы, в которых существуют Службы мультимедиа Azure версии 3

Службы мультимедиа Azure версии 3 доступны через манифест диспетчера ресурсов Azure в глобальных Azure, Azure для государственных организаций, Azure для Германии, Azure China 21Vianet. Однако не все функции Служб мультимедиа доступны во всех облаках Azure. В этом документе описывается наличие основных компонентов Служб мультимедиа версии 3.

## <a name="feature-availability-in-azure-clouds"></a>Доступность функций в облаках Azure

| Функция|Глобальные регионы Azure | Azure Government|Azure для Германии|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Доступна | Недоступно | Недоступно | Недоступно |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Доступна | Недоступно | Недоступно | Недоступно |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Доступна | Недоступно | Недоступно | Недоступно |
| [StandardEncoderPreset](encoding-concept.md) | Доступна | Доступна | Доступна | Доступна |
| [LiveEvents](live-streaming-overview.md) | Доступна | Доступна | Доступна | Доступна |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Доступна | Доступна | Доступна | Доступна |

## <a name="regionsgeographieslocations"></a>Регионы, географические регионы или расположения

* [Регионы Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Продукты по регионам](https://azure.microsoft.com/global-infrastructure/services/)
* [Географические регионы Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Кодовое имя региона 

Если необходимо предоставить параметр **расположение**, нужно указать имя и код региона в качестве значения **location**. Для получения кодового имени региона, в котором развернута ваша учетная запись и к которому должен быть направлен ваш вызов, выполните следующую строку в [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

```bash
az account list-locations
```

Когда вы запустите показанную выше строку, вы увидите список всех регионов Azure. Перейдите к региону Azure со значением *displayName*, которое вы ищете, и используйте его *имя* для параметра **location**.

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

|Конечные точки ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| Аудитория токена | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Конечные точки||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| Аудитория токена | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure для Германии

| Конечные точки ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Аудитория токена | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Конечные точки||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Аудитория токена |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>Дополнительная информация

[Что такое Службы мультимедиа Azure версии 3?](media-services-overview.md)
