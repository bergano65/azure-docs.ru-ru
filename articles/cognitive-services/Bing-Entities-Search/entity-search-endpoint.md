---
title: Конечная точка API Bing для поиска сущностей
titlesuffix: Azure Cognitive Services
description: Сведения о конечной точке API Bing для поиска сущностей и отправке запросов к ней.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 3c2aa4b22c8e679f73692978d9e1f8009f11a46b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875235"
---
# <a name="bing-entity-search-api-endpoint"></a>Конечная точка API Bing для поиска сущностей


API Bing для поиска сущностей использует одну конечную точку, которая возвращает сущности из Интернета на основе запроса. Результаты поиска возвращаются в формате JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Получение результатов поиска сущностей из конечной точки

Чтобы получить результаты поиска сущностей с помощью **API Bing**, отправьте запрос `GET` на следующую конечную точку. Используйте [заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) и [параметры запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) для настройки поискового запроса. Поисковые запросы можно отправлять с помощью параметра `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные сведения об API Bing для поиска сущностей](overview.md)

## <a name="see-also"></a>См. также 

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. д. приведены в статье [Bing Entity Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) (Справочник по API Bing для поиска сущностей версии 7).
