---
title: Поиск набирающих популярность видео в Интернете с помощью API Bing для поиска видео
titlesuffix: Azure Cognitive Services
description: Узнайте, как с помощью API Bing для поиска видео искать в Интернете видео, набирающие популярность.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2aa3c0cd9112f2de9ccda818e74b0b2c377133bf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869370"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Поиск видео, набирающего популярность, с помощью API Bing для поиска видео 

API Bing для поиска видео позволяет искать в Интернете видео, набирающие сегодня популярность, в разных категориях. 

## <a name="get-request"></a>Запрос GET

Чтобы получить видео, набирающие сегодня популярность, от API Bing для поиска видео, отправьте следующий запрос GET.  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Поддержка рынков

Поиск видео, набирающих популярность, поддерживается для следующих рынков:  
 
-   en-AU (английский, Австралия)  
-   en-CA (английский, Канада)  
-   en-GB (английский, Великобритания)  
-   en-ID (английский, Индонезия)  
-   en-IE (английский, Ирландия)  
-   en-IN (английский, Индия)  
-   en-NZ (английский, Новая Зеландия)  
-   en-PH (английский, Филиппины)  
-   en-SG (английский, Сингапур)  
-   en-US (английский, США)  
-   en-WW (английский, объединенный глобальный код)  
-   en-ZA (английский, Южная Африка)  
-   zh-CN (китайский, Китай)

## <a name="example-json-response"></a>Пример ответа в формате JSON  

В примере показан ответ API, содержащий видео, набирающие популярность, которые перечислены по категориям и подкатегориям. Ответ также содержит баннеры с видео, которые являются самыми популярными видео в одной или нескольких категориях.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
[Получение полезных сведений о видео](video-insights.md)