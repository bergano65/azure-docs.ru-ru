---
title: Как страницу через результаты поиска - Bing Поиск AIS
titleSuffix: Azure Cognitive Services
description: Узнайте, как просматривать результаты поиска с помощью ApIs поиска Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481729"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Как просматривать результаты apIs поиска Bing

При отправке вызова в ATUBE Bing Web, Custom, Image, News или Video Search Bing возвращает подмножество общего числа результатов, которые могут иметь отношение к запросу. Чтобы получить предполагаемое общее количество доступных результатов, получите доступ к `totalEstimatedMatches` полю объекта ответа. 

Пример: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Прогон с помощью результатов поиска

Чтобы получить страницу по `count` имеющимся результатам, используйте параметры `offset` запроса при отправке запроса.  

> [!NOTE]
>
> * Paging с Bing Video, Image и News AIS применяется только к общему видео (),`/video/search`новости ( )`/news/search`и изображения ( )`/image/search`поиск. Прописка по трендовым темам и категориям не поддерживается.  
> * Поле `TotalEstimatedMatches` представляет собой оценку общего числа результатов поиска для текущего запроса. При настройке `count` `offset` параметров эта оценка может измениться.

| Параметр | Описание                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Задает количество результатов, возвращаемых в ответе. Обратите `count`внимание, что значение значения по умолчанию и максимальное количество результатов, которые вы можете запросить, варьируются в зависимости от API. Эти значения можно найти в справочной документации в соответствии с [следующими шагами.](#next-steps) |
| `offset`  | Задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).                                           |

Например, если вы хотите отобразить 15 `count` результатов на `offset` страницу, вы установите 15 и до 0, чтобы получить первую страницу результатов. Для каждого последующего вызова API `offset` вы будете приравливать на 15. В следующем примере запрашиваются 15 веб-страниц с начальным смещением в 45 результатов.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

При использовании `count` значения по умолчанию необходимо `offset` указать параметр запроса в вызовах API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

При использовании AAP-изображений и видео `nextOffset` Bing можно использовать значение, чтобы избежать дублирования результатов поиска. Получите значение `Images` от `Videos` объектов или ответов и `offset` используйте его в своих запросах с параметром.  

> [!NOTE]
> API поиска в Интернете возвращает результаты поиска, которые могут включать веб-страницы, изображения, видео и новости. Когда вы просматриваете результаты поиска с помощью API веб-поиска Bing, вы просматриваете только [WebPages,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)а не другие типы ответов, такие как изображения или новости. Результаты `WebPage` поиска на объектах могут включать результаты, которые отображаются и в других типах ответов.
>
> Если вы `responseFilter` используете параметр запроса без указания значений `count` фильтра, не используйте параметры и `offset` параметры. 

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое AIS веб-поиска Bing?](bing-api-comparison.md)
* [Справка по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Ссылка на API пользовательского поиска Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing Новости Поиск API v7 ссылка](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Видео Поиск API v7 ссылка](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Ссылка на API поиска изображений Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
