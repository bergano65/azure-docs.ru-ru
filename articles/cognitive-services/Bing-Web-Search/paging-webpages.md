---
title: Постраничный просмотр результатов поиска — API Bing для поиска в Интернете
titleSuffix: Azure Cognitive Services
description: Узнайте, как просматривать результаты поиска постранично в API Bing для поиска в Интернете.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384815"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Как просматривать результаты постранично в API Bing для поиска в Интернете

Когда вы вызываете API Bing для поиска в Интернете, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Чтобы получить предполагаемое общее количество доступных результатов, необходимо обратиться к полю [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) объекта ответа.  

В следующем примере показано поле `totalEstimatedMatches`, содержащееся в результатах поиска в Интернете.  

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

Чтобы разбить на страницы список доступных веб-страниц, используйте параметры запроса [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset).  

Параметр `count` задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 50. По умолчанию задано значение 10. Фактическое число полученных результатов может быть меньше запрошенного.

Параметр `offset` задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).  

Если вы хотите отображать 15 веб-страниц на странице, необходимо задать для параметра `count` значение 15, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 15 (например, 15, 30).  

В следующем примере запрашиваются 15 веб-страниц с начальным смещением в 45 результатов.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Если значение по умолчанию `count` подходит для вашей реализации, необходимо указать только параметр запроса `offset`.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

API для поиска в Интернете возвращает результаты, которые включают в себя веб-страницы и могут также включать изображения, видео и новости. При разбиении на страницы результатов поиска используется ответ [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer), но не другие ответы (изображения или новости). Например, если задать для параметра `count` значение 50, будут возвращены 50 найденных веб-страниц, однако ответ может содержать и результаты других типов. Например, ответ может включать в себя 15 изображений и 4 статьи. Возможно также, что на первой странице результатов будут отображены новости, но их не будет на второй странице, или наоборот.   

Если вы указали параметр запроса `responseFilter` и не добавили Webpages в список фильтров, не используйте параметры `count` и `offset`. 

> [!NOTE]
> Поле `TotalEstimatedAnswers` содержит приблизительное общее число результатов поиска, которые вы можете получить для текущего запроса.  При задании параметров `count` и `offset` число `TotalEstimatedAnswers` может измениться. 

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое веб-API поиска Bing](overview.md)?
