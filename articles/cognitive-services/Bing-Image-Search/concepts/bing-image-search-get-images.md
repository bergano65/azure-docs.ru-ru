---
title: Получение изображений из Интернета — API Bing для поиска изображений
titleSuffix: Azure Cognitive Services
description: Используйте API Bing для поиска изображений и получайте соответствующие изображения из Интернета.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 309bbca762149f8804742d9ef02d4c3e8dfcdc6b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542763"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Получение изображений из Интернета с помощью API Bing для поиска изображений

При использовании REST API Bing для поиска изображений можно получать изображения из Интернета, которые связаны с условием вашего поиска путем отправки следующего GET-запроса:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Используйте [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) параметр запроса для условия поиска url адреса. Например, если вы вводите *sailing dinghies*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

> [!IMPORTANT]
> * Все запросы нужно выполнить на сервере, а не в клиенте.
> * Если вы впервые вызываете любой из поисковых API-интерфейсов Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента только в том случае, если вы ранее вызывали API Bing, который возвратил идентификатор клиента для определенного сочетания пользователя и устройства.

## <a name="get-images-from-a-specific-web-domain"></a>Получение изображений из определенного веб-домена

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Ответы на запросы с помощью оператора `site:` могут относиться к содержимому для взрослых вне зависимости от параметров [SafeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch). Используйте `site:` только тогда, когда вам известно содержимое домена.

## <a name="filter-images"></a>Фильтрация изображений

 По умолчанию API для поиска изображений возвращает все изображения, относящиеся к запросу. Если необходимо использовать фильтры для изображений, которые возвращает Bing (например, только изображения с прозрачным фоном или определенного размера), используйте следующие параметры запроса:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect) — фильтрация изображений по формату (например, стандартные или широкоэкранные изображения);
* [color](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color) — фильтрация изображений по доминирующему цвету или черно-белой палитре;
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness) — фильтрация изображений по сроку существования (например, изображения, обнаруженные Bing на прошлой неделе);
* [height](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width) — фильтрация изображений по ширине и высоте;
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent) — фильтрация изображений по содержанию (например, изображения, на которых только лицо человека);
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) — фильтрация изображений по типу (например, клип, GIF с анимацией или с прозрачным фоном);
* [license](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) — фильтрация изображений по типу лицензии, связанной с сайтом;
* [size](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size) — фильтрация изображений по размеру, например маленькие изображения размером до 200x200 пикселей.

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](https://msdn.microsoft.com/library/ff795613.aspx).

В следующем примере показано, как получить небольшие изображения с сайта ContosoSailing.com, обнаруженные Bing на прошлой неделе.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Формат ответа для API Bing для поиска изображений

Ответное сообщение от Bing содержит [Изображения](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images), список которых определен в Cognitive Services как соответствующий запросу. Каждый объект [Изображения](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) в списке содержит следующие сведения об изображении: URL-адрес, размер, измерения, формат кодирования, URL-адрес эскиза изображения, а также размеры эскизов.

> [!NOTE]
> * Изображения должны отображаться в порядке, указанном в ответе.
> * Так как форматы URL-адрес и параметры могут измениться в любое время без предварительного уведомления, используйте все URL-адреса как-является. Не должен принимать зависимости от формата URL-адреса или параметры, за исключением оговоренных.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Когда вы вызываете API Bing для поиска изображений, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Поле ответа `totalEstimatedMatches` содержит оценку количества изображений, доступных для просмотра. Подробнее о том, как просматривать оставшиеся изображения, см. в статье [Разбиение результатов по страницам](../paging-images.md).

## <a name="next-steps"></a>Дальнейшие действия

Если вы еще ни разу не использовали API Bing для поиска изображений, ознакомьтесь со следующей [статьей](../quickstarts/csharp.md). Если вы ищете что-нибудь посложнее, ознакомьтесь с руководством по созданию [одностраничного веб-приложения](../tutorial-bing-image-search-single-page-app.md).
