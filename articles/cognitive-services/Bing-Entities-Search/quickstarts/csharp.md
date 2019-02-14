---
title: Краткое руководство. Отправка запросов для поиска в REST API Поиска сущностей Bing с помощью C#
titlesuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API Поиска сущностей Bing с помощью C# и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b61db97cec77fc724933c2b4e7d3fa7f7afc0ab6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884959"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Краткое руководство. Отправка запросов для поиска в REST API Поиска сущностей Bing с помощью C#

Из этого краткого руководства вы узнаете, как вызвать API Поиска сущностей Bing и просмотреть ответ в формате JSON. Это простое приложение C# отправляет запрос на поиск новостей к API и отображает ответ. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.


## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Платформа [Json.NET](https://www.newtonsoft.com/json), доступная в виде пакета NuGet.
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](http://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

1. Создайте консольное приложение C# в Visual Studio. Затем добавьте следующие пространства имен в основной файл кода.
    
    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Создайте класс и добавьте переменные для конечной точки API, ключ подписки и запрос для поиска.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Отправка запроса и получение ответа API

1. В классе создайте функцию с именем `Search()`. Создайте объект `HttpClient` и добавьте к заголовку ключ подписки `Ocp-Apim-Subscription-Key`.

    1. Создайте универсальный код ресурса (URI) для вашего запроса, объединив хост и путь и добавив market и URL-кодирование своего запроса.
    2. Получите HTTP-ответ с помощью метода `client.GetAsync()`, а затем сохраните его в формате JSON с помощью метода `ReadAsStringAsync()`.
    3. Выведите строку в консоль.

    ```csharp
    async static void Search()
    {
        //...
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

        HttpResponseMessage response = await client.GetAsync(uri);

        string contentString = await response.Content.ReadAsStringAsync();
        Console.WriteLine(JsonPrettyPrint(contentString));
    }
    ```

2. В методе main приложения вызовите функцию `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
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
        "url": "http://www.princi.com/",
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
