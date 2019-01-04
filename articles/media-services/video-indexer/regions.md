---
title: Регионы Azure, в которых доступен Индексатор видео
titlesuffix: Azure Media Services
description: В этой статье рассказывается о регионах Azure, в которых доступен Индексатор видео.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/08/2018
ms.author: anzaman
ms.openlocfilehash: 02872afed03494962d37e76ba0d8da524fb7b3a8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283740"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Регионы Azure, в которых доступен Индексатор видео

API Индексатора видео содержат параметр **location**, для которого следует задать регион Azure, к которому должен направляться вызов. Это должен быть [регион Azure, в котором доступен Индексатор видео](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Расположения

В качестве значения параметру **location** необходимо присвоить имя кода региона Azure. Если вы используете Индексатор видео в режиме предварительного просмотра, следует указывать *trial* как значение. В противном случае для получения имени кода региона Azure, в котором развернута ваша учетная запись и к которому должен быть направлен ваш вызов, выполните следующую строку в [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
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

## <a name="next-steps"></a>Дополнительная информация

- [Customize a Language model with the Video Indexer APIs](customize-language-model-with-api.md) (Настройка языковой модели с помощью API Индексатора видео)
- [Customize a Brands model with the Video Indexer API](customize-brands-model-with-api.md) (Настройка модели торговых марок с помощью API Индексатора видео)
- [Customize a Person model with the Video Indexer API](customize-person-model-with-api.md) (Настройка модели Person с помощью API Индексатора видео)