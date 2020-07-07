---
title: Регионы Azure, в которых доступен Индексатор видео
titleSuffix: Azure Media Services
description: В этой статье рассказывается о регионах Azure, в которых доступен индексатор видео служб мультимедиа Azure.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b71890323f6aca92fb9f847252feed57d8db234f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594956"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Регионы Azure, в которых доступен Индексатор видео

API Индексатора видео содержат параметр **location**, для которого следует задать регион Azure, к которому должен направляться вызов. Это должен быть [регион Azure, в котором доступен Индексатор видео](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Расположения

`location`Параметру должно быть присвоено имя кода региона Azure в качестве значения. При использовании индексатора видео в режиме предварительного просмотра следует поместиться в `"trial"` качестве значения. `trial`значение по умолчанию для `location` параметра. В противном случае для получения имени кода региона Azure, в котором развернута ваша учетная запись и к которому должен быть направлен ваш вызов, выполните следующую строку в [Azure CLI](/cli/azure):

```azurecli-interactive
az account list-locations
```

Когда вы запустите показанную выше строку, вы увидите список всех регионов Azure. Перейдите к региону Azure со значением *displayName*, которое вы ищете, и используйте его *имя* для параметра **location**.

Например, для региона Azure "западная часть США 2" (показан ниже) для параметра **location** нужно использовать значение westus2.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Дальнейшие действия

- [Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео)
- [Customize a Brands model with the Video Indexer API](customize-brands-model-with-api.md) (Настройка модели торговых марок с помощью API Индексатора видео)
- [Customize a Person model with the Video Indexer API](customize-person-model-with-api.md) (Настройка модели Person с помощью API Индексатора видео)
