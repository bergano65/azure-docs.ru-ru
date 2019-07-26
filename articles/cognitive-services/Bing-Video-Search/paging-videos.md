---
title: Постраничное отображение доступных видео — Поиск видео Bing
titleSuffix: Azure Cognitive Services
description: Узнайте, как разбить на страницы список видео, возвращаемый API Bing для поиска видео, для удобного просмотра.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500706"
---
# <a name="paging-through-video-search-results"></a>Разбиение на страницы результатов поиска видео

API Bing для поиска видео возвращает подмножество всех результатов поиска, найденных по вашему запросу. Разбивая эти результаты на страницы для последующих вызовов API, можно получить их и отобразить в своем приложении.

> [!NOTE]
> Разбиение на страницы доступно только для поиска видео (/videos/search) и не применимо к аналитическим сведениям о видео (/videos/details) или видео, набирающим популярность (/videos/trending).

## <a name="total-estimated-matches"></a>Общее число предполагаемых совпадений

Чтобы получить предполагаемое количество найденных результатов поиска, используйте поле [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) в ответе, представленном в формате JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Разбиение на страницы списка видео

Чтобы разбить на страницы список доступных видео, используйте в запросе параметры [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset).  
  

|Параметр  |Описание  |
|---------|---------|
|`count`     | Задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 100. По умолчанию задано значение 10. Фактическое число полученных результатов может быть меньше запрошенного.        |
|`offset`     | Задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).          |

Например, если вы хотите отображать 20 статей на странице, необходимо задать для параметра `count` значение 20, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 20 (например, 20, 40).  
  
В следующем примере запрашиваются 20 видео с начальным смещением в 40 результатов.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Если вы используете значение по умолчанию для параметра [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), необходимо указать только параметр `offset`, как показано в следующем примере запроса.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Для разбиения списка на страницы по 35 видео на каждой странице для параметра `offset` необходимо при первом запросе задать значение 0, а затем увеличивать `offset` на 35 при каждом последующем запросе. Тем не менее некоторые результаты в следующем ответе могут содержать повторяющиеся видео из предыдущего ответа. Например, первые два видео в ответе могут совпадать с последними двумя видео из предыдущего ответа.

Чтобы исключить повторяющиеся результаты, используйте поле [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) объекта `Videos`.

Например, для разбиения по 30 видео на страницу при первом запросе необходимо задать для параметра `count` значение 30, а для `offset` — 0. При следующем запросе следует задать для параметра `offset` значение, равное `nextOffset`.

> [!NOTE]
> Поле `TotalEstimatedAnswers` содержит приблизительное общее число результатов поиска, которые вы можете получить для текущего запроса.  При задании параметров `count` и `offset` число `TotalEstimatedAnswers` может измениться. 
  
## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение полезных сведений о видео](video-insights.md)
