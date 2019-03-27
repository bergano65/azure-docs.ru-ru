---
title: Краткое руководство. Отправка запросов для поиска в REST API Bing для поиска сущностей с помощью Node.js
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API Bing для поиска сущностей с помощью C# и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 8007d576a6b896f12423087cfd4a483d9171abc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104375"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Краткое руководство. Отправка запросов для поиска в REST API Bing для поиска сущностей с помощью Node.js

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска сущностей и просмотреть ответ в формате JSON. Это простое приложение JavaScript отправляет запрос на поиск новостей к API и отображает ответ. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Хотя это приложение создается на языке JavaScript, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/en/download/).

* [Библиотека запросов JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и установите степень строгости, а также требования к HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Создайте переменные для конечной точки API, ключа подписки и поискового запроса.

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

1. Определите функцию с именем `response_handler`, принимающую HTTP-вызов `response` как параметр. Выполните следующие действия в этой функции.

    1. Определите переменную для хранения текста ответа JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Сохраните текст ответа при вызове флага **data**.
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. При активации флага **end** можно проанализировать и распечатать код JSON.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Отправка запроса

1. Создайте функцию `Search`, чтобы отправить поисковый запрос. В ней необходимо выполнить следующие действия.

   1. Создать объект JSON, содержащий параметры запроса. Используйте команду `Get` для метода, а затем добавьте сведения об узле и пути. Добавьте ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. 
   2. Чтобы отправить запрос с помощью обработчика ответов, созданного ранее, и параметров поиска, используйте метод `https.request()`.
    
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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям для наглядного поиска](../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../overview.md )
* [Справочник по API Bing для поиска сущностей](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
