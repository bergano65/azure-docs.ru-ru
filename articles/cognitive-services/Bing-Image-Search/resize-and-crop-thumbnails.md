---
title: Изменение размера и обрезка эскизов — API Bing для поиска изображений
titleSuffix: Azure Cognitive Services
description: Изменение размера и обрезка эскизов в ответе от API Bing для поиска изображений.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c53b84f31100ab72458ab6fb79b2009450eda15c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914591"
---
# <a name="resize-and-crop-thumbnail-images"></a>Изменение размера и обрезка эскизов

При обработке поискового запроса Bing создаст информацию эскизов для всех образов в [ответе](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Эти сведения можно использовать для отображения всех или подмножества возвращенных эскизов. Если можно отображать подмножество, предоставляет возможность просматривать остальные изображения.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Если пользователь щелкает эскиз, вы можете использовать [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) для отображения полноразмерного изображения пользователю. Обязательно описывайте изображение.

Если `shoppingSourcesCount` или `recipeSourcesCount` больше нуля, добавьте значки, например тележку для покупок, на эскиз, чтобы указать, что для объекта на изображении существуют покупки или квитанции.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Чтобы получить представление об изображении, например о веб-страницах, содержащих изображение, или людях, которые были распознаны на изображении, используйте [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Дополнительные сведения см. в статье [Get insights about an image](image-insights.md) (Получение аналитических сведений об изображении).

## <a name="resizing-and-cropping-thumbnails"></a>Изменение размера и обрезка эскизов

Можно также изменить размер и развернуть эскизы, например, когда пользователь наводит на них курсор.
> [!NOTE]
> Обязательно описывайте изображение, если оно будет раскрываться. Например, извлекая узел из [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) и отображая его под изображением.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
