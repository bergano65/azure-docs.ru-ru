---
title: Просмотр результатов поиска с помощью API-интерфейсы поиска Bing
titleSuffix: Azure Cognitive Services
description: Сведения о просмотре результатов поиска из API-интерфейсы поиска Bing.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73481729"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Как пролистывать результаты из API-интерфейсы поиска Bing

При отправке вызова в API Bing для веб-приложений, пользовательских интерфейсов, изображений, новостей или Поиск видеоов Bing возвращает подмножество общего количества результатов, которые могут быть актуальны для запроса. Чтобы получить оценочное общее число доступных результатов, получите доступ к `totalEstimatedMatches` полю объекта ответа. 

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

## <a name="paging-through-search-results"></a>Разбиение по страницам результатов поиска

Чтобы просмотреть доступные результаты, используйте параметры запроса `count` и `offset` при отправке запроса.  

> [!NOTE]
>
> * Подкачка интерфейсов API Bing Video, Image и News применяется только к основным операциям`/video/search`поиска видео ()`/news/search`, новостей ()`/image/search`и Image (). Разбиение по страницам разделов и категорий тенденций не поддерживается.  
> * `TotalEstimatedMatches` Поле представляет собой оценку общего количества результатов поиска для текущего запроса. При задании параметров `count` и `offset` эта оценка может измениться.

| Параметр | Описание                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Задает количество результатов, возвращаемых в ответе. Обратите внимание, что значение `count`по умолчанию и максимальное количество результатов, которые можно запросить, зависят от API. Эти значения можно найти в справочной документации в разделе [дальнейшие действия](#next-steps). |
| `offset`  | Задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).                                           |

Например, если вы хотите отобразить 15 результатов на странице, для получения первой страницы результатов необходимо `count` задать значение 15 `offset` и значение 0. Для каждого последующего вызова API значение будет увеличено `offset` на 15. В следующем примере запрашиваются 15 веб-страниц с начальным смещением в 45 результатов.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Если используется значение по умолчанию `count` , необходимо только указать параметр `offset` запроса в вызовах API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

При использовании API-интерфейсов изображений и видео Bing можно использовать `nextOffset` значение, чтобы избежать дублирования результатов поиска. Получите значение из объектов ответов `Images` или `Videos` и используйте его в запросах с `offset` параметром.  

> [!NOTE]
> API Bing для поиска в Интернете возвращает результаты поиска, которые могут включать веб-страницы, изображения, видео и новости. При просмотре результатов поиска из API Bing для поиска в Интернете отображаются только [веб-страницы](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), а не другие типы ответов, такие как изображения или Новости. Результаты поиска в `WebPage` объектах могут содержать результаты, которые также отображаются в других типах ответов.
>
> Если вы используете параметр `responseFilter` запроса без указания каких бы то ни было значений фильтра, `count` не `offset` используйте параметры и. 

## <a name="next-steps"></a>Следующие шаги

* [Что такое Поиск в Интернете Bing API?](bing-api-comparison.md)
* [Справка по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Справочник по API пользовательского поиска Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Справочник по API Bing для поиска новостей версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Справочник по API Bing для поиска видео версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Справочник по API Bing для поиска изображений версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
