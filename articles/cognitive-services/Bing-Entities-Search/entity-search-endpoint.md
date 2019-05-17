---
title: Конечная точка API Bing для поиска сущностей
titlesuffix: Azure Cognitive Services
description: Сведения о конечной точке API Bing для поиска сущностей и отправке запросов к ней.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b29e568d6b3b1382b5be434500014f10740b58f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788544"
---
# <a name="bing-entity-search-api-endpoint"></a>Конечная точка API Bing для поиска сущностей


API Bing для поиска сущностей использует одну конечную точку, которая возвращает сущности из Интернета на основе запроса. Результаты поиска возвращаются в формате JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Получение результатов поиска сущностей из конечной точки

Чтобы получить результаты поиска сущностей с помощью **API Bing**, отправьте запрос `GET` на следующую конечную точку. Используйте [заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) и [параметры запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters) для настройки поискового запроса. Поисковые запросы можно отправлять с помощью параметра `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные сведения об API Bing для поиска сущностей](overview.md)

## <a name="see-also"></a>См. также 

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. д. приведены в статье [Bing Entity Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) (Справочник по API Bing для поиска сущностей версии 7).
