---
title: Облака и регионы, в которых доступны службы мультимедиа Azure v3
description: В этой статье рассказывается об облаках Azure и регионах, в которых доступны Службы мультимедиа Azure версии 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382976"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Облака и регионы, в которых существуют Службы мультимедиа Azure версии 3

Службы мультимедиа Azure версии 3 доступны через манифест диспетчера ресурсов Azure в глобальных Azure, Azure для государственных организаций, Azure для Германии, Azure China 21Vianet. Однако не все функции Служб мультимедиа доступны во всех облаках Azure. В этом документе описывается наличие основных компонентов Служб мультимедиа версии 3.

## <a name="feature-availability-in-azure-clouds"></a>Доступность функций в облаках Azure

| Компонент|Глобальные регионы Azure | Azure для государственных организаций|Azure для Германии|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Доступно | Недоступно | Недоступно | Недоступно |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Доступно | Недоступно | Недоступно | Недоступно |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Доступно | Недоступно | Недоступно | Недоступно |
| [StandardEncoderPreset](encoding-concept.md) | Доступно | Доступно | Доступно | Доступно |
| [LiveEvents](live-streaming-overview.md) | Доступно | Доступно | Доступно | Доступно |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Доступно | Доступно | Доступно | Доступно |

## <a name="regionsgeographieslocations"></a>Регионы, географические регионы или расположения

[Регионы, в которых развернута служба служб мультимедиа Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Кодовое имя региона

Если необходимо предоставить параметр **расположение**, нужно указать имя и код региона в качестве значения **location**. Для получения кодового имени региона, в котором развернута ваша учетная запись и к которому должен быть направлен ваш вызов, выполните следующую строку в [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

```azurecli-interactive
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

|Конечные точки||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Проверка подлинности | `https://login.microsoftonline.com/` |
| Аудитория токена | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure для государственных организаций

|Конечные точки||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Проверка подлинности | `https://login.microsoftonline.us/` |
| Аудитория токена | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure для Германии

| Конечные точки ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Проверка подлинности | `https://login.microsoftonline.de/` |
| Аудитория токена | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Конечные точки||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Проверка подлинности | `https://login.chinacloudapi.cn/` |
| Аудитория токена |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>См. также

* [Регионы Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Географические регионы Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Дальнейшие шаги

[Что такое Службы мультимедиа Azure версии 3?](media-services-overview.md)
