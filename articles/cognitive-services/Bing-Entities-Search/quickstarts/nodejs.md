---
title: Краткое руководство. Отправка запросов для поиска в REST API Поиска сущностей Bing с помощью Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API "Поиск сущностей Bing" с помощью Node.js и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 9e827acf6a0103b78b3d7713d54af927722a5911
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334792"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Краткое руководство. Отправка запросов для поиска в REST API Bing для поиска сущностей с помощью Node.js

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска сущностей и просмотреть ответ в формате JSON. Это простое приложение JavaScript отправляет запрос на поиск новостей к API и отображает ответ. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Это приложение написано на JavaScript. Но API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/en/download/).

* [Библиотека запросов JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в используемых вами интегрированной среде разработки или редакторе, а также укажите степень строгости и требования к HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Создайте переменные для конечной точки API, ключа подписки и поискового запроса. Вы можете использовать глобальную конечную точку, указанную в коде ниже, или конечную точку [личного поддомена](../../../cognitive-services/cognitive-services-custom-subdomains.md), которая отображается на портале Azure для вашего ресурса.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Добавьте параметры рынка и запроса в строку `query`. Не забудьте выполнить URL-кодирование запроса с помощью `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Обработка и анализ ответа

1. Определите функцию с именем `response_handler()`, принимающую HTTP-вызов `response` как параметр. 

2. В этой функции определите переменную для хранения текста ответа в формате JSON.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Сохраните текст ответа при вызове флага `data`.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. При активации флага `end` можно проанализировать и распечатать код JSON.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
        ```

## Send a request

1. Create a function called `Search()` to send a search request. In it, perform the following steps:

2. Within this function, create a JSON object containing your request parameters. Use `Get` for the method, and add your host and path information. Add your subscription key to the `Ocp-Apim-Subscription-Key` header. 

3. Use `https.request()` to send the request with the response handler created previously, and your search parameters.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. Вызовите функцию `Search()`.

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

* [Основные сведения об API Bing для поиска сущностей](../overview.md )
* [Справочник по API "Поиск сущностей Bing"](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
