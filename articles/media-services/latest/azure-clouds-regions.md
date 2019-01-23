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
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: 8eb49010d89c3039f46e5c84cd305b7d0b5ca025
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306990"
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

## <a name="regions"></a>регионы 

Если необходимо предоставить параметр **расположение**, нужно указать имя и код региона в качестве значения **location**. Для получения имени кода региона, в котором развернута ваша учетная запись и к которому должен быть направлен ваш вызов, выполните следующую строку в [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

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

## <a name="next-steps"></a>Дополнительная информация

[Что такое Службы мультимедиа Azure версии 3?](media-services-overview.md)
