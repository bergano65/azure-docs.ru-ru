---
title: Краткое руководство. Отправка поисковых запросов в REST API Поиск сущностей Bing с помощью Python
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API Bing для поиска сущностей с помощью Python и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 53f9badbb15c788606390921cb8ef62495c19839
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852758"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Краткое руководство. Отправка запросов для поиска в REST API Bing для поиска сущностей с помощью Python

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска сущностей и просмотреть ответ в формате JSON. Это простое приложение Python отправляет запрос на поиск новостей к API и отображает ответ. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Это приложение создано на языке Python. Но API представляет собой веб-службу на основе REST, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

* [Python](https://www.python.org/downloads/) 2.x или 3.x.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Python в избранной интегрированной среде разработки или редакторе и добавьте следующие операции импорта. Создайте переменные для вашего ключа подписки, конечной точки, рынка и поискового запроса. Вы можете использовать глобальную конечную точку, указанную в коде ниже, или конечную точку [личного поддомена](../../../cognitive-services/cognitive-services-custom-subdomains.md), которая отображается на портале Azure для вашего ресурса.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Создайте URL-адрес запроса, добавив свою рыночную переменную к параметру `?mkt=`. Выполните кодирование URL-адреса вашего поискового запроса и добавьте его к параметру `&q=`. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Отправка запроса и получение ответа

1. Создайте функцию `get_suggestions()`. 

2. В этой функции добавьте ключ подписки в словарь, используя `Ocp-Apim-Subscription-Key` в качестве ключа.

3. Используйте `http.client.HTTPSConnection()`, чтобы создать клиентский объект HTTPS. Отправьте запрос `GET` с помощью `request()`, указав сведения о пути, параметрах и заголовке.

4. Сохраните ответ с помощью `getresponse()` и верните `response.read()`.

   ```python
   def get_suggestions ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read()
   ```

5. Вызовите `get_suggestions()` и выведите ответ в формате JSON.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

## <a name="example-json-response"></a>Пример ответа в формате JSON

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям для наглядного поиска](../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../search-the-web.md)
* [Справочник по API "Поиск сущностей Bing"](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
