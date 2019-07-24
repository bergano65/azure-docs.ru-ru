---
title: Разбивка на страницы результатов, возвращаемых Поиском новостей Bing
titleSuffix: Azure Cognitive Services
description: Узнайте, как разбить на страницы новостные статьи, возвращаемые API Bing для поиска новостей.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423751"
---
# <a name="how-to-page-through-news-search-results"></a>Разбивка на страницы с помощью результатов поиска новостей

Когда вы вызываете API Bing для поиска новостей, Bing возвращает список результатов, которые относятся к запросу. Чтобы получить предполагаемое общее количество доступных результатов, необходимо обратиться к полю [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) объекта ответа.  
  
В следующем примере показано поле `totalEstimatedMatches`, содержащееся в результатах поиска новостей.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Чтобы разбить на страницы список доступных статей, используйте параметры запроса [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset).  
 

|Параметр  |Описание  |
|---------|---------|
|`count`     | Задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 100. По умолчанию задано значение 10. Фактическое число полученных результатов может быть меньше запрошенного.        |
|`offset`     | Задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).          |

Например, если вы хотите отображать 20 статей на странице, необходимо задать для параметра `count` значение 20, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 20 (например, 20, 40).  
  
В следующем примере запрашиваются 20 новостных статей с начальным смещением в 40 результатов.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Если значение по умолчанию `count` подходит для вашей реализации, необходимо указать только параметр запроса `offset`, как показано в следующем примере:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Разбиение на страницы доступно только для поиска новостей (/news/search) и не применимо к темам, набирающим популярность (/news/trendingtopics), или категориям новостей (/news).

> [!NOTE]
> Поле `TotalEstimatedAnswers` содержит приблизительное общее число результатов поиска, которые вы можете получить для текущего запроса.  При задании параметров `count` и `offset` число `TotalEstimatedAnswers` может измениться. 
