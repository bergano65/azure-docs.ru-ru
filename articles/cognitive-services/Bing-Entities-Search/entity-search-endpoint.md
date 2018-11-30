---
title: Конечная точка API Bing для поиска сущностей
titlesuffix: Azure Cognitive Services
description: Сводные сведения о конечной точке API для поиска сущностей.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: aahi
ms.openlocfilehash: 578791c8beb34b08b28037977147d41db83f4a4d
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164922"
---
# <a name="entity-search-endpoints"></a>Конечные точки для поиска сущностей
**API для поиска сущностей** включает одну конечную точку.

## <a name="endpoint"></a>Конечная точка
Чтобы запросить результаты поиска сущностей, отправьте запрос в следующую конечную точку. Для определения дополнительных спецификаций используйте заголовки и параметры URL-адреса.

Конечная точка `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Требуются следующие параметры URL-адреса:
- mkt. Рынок, для которого будут отображаться результаты. 
- q. Запрос на поиск сущностей.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткие руководства по поиску сущностей Bing](quickstarts/csharp.md)

## <a name="see-also"></a>См. также 

[Общие сведения о поиске сущностей Bing](search-the-web.md )
[Справочник по API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
