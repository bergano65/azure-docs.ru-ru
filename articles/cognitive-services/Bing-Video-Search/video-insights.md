---
title: Получение полезных сведений о видео с помощью API Bing для поиска видео
titlesuffix: Azure Cognitive Services
description: Узнайте, как использовать API Bing для поиска видео для получения дополнительных сведений о видео, в том числе связанных видео.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c5f3a43a6c48714566b5c53b33fe541b50d61589
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383895"
---
# <a name="get-insights-about-a-video"></a>Получение полезных сведений о видео

Каждое видео, возвращаемое API Bing для поиска видео, содержит идентификатор, с помощью которого можно получить дополнительные сведения об этом видео, например найти связанные видео. Чтобы получить полезные сведения о видео, получите токен [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid), указанный в ответе API. 

```json
    "value" : [
        {
            . . .
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear...",
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHBZ--g",
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63",
            . . .
        }
    ],
```

Затем отправьте запрос GET к конечной точке сведений о видео, указав этот идентификатор. Укажите для параметра запроса [id](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#id) значение токена `videoId`. Чтобы указать полезные сведения, которые необходимо получить, задайте значение параметра запроса [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested). Чтобы получить все сведения, задайте для `modules` значение All. Ответ будет содержать все сведения, которые вы запросили, если они доступны.

```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-related-videos-insights"></a>Получение полезных сведений о связанных видео  

Чтобы найти видео, связанные с указанным видео, задайте для параметра запроса [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested) значение `RelatedVideos`.
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=RelatedVideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Ответ на этот запрос будет содержать объект верхнего уровня [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails) вместо объекта [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos).  
  
```json
{
    "_type" : "Api.VideoDetails.VideoDetails",
    "relatedVideos" : {
        "value" : [
            {
                "name" : "How to sail - Reefing a Sail",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OVP.zt...",
                "datePublished" : "2014-03-04T16:11:09",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?...",
                "duration" : "PT4M56S",
                "motionThumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OM...",
                "allowHttpsEmbed" : true,
                "viewCount" : 21756,
                "videoId" : "AC1A157A4DDB571D03D6AC157A4DDB571D03D6",
                "allowMobileEmbed" : false,
                "isSuperfresh" : false
            },
            . . .
        ]
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Поиск видео, набирающих популярность](trending-videos.md)

