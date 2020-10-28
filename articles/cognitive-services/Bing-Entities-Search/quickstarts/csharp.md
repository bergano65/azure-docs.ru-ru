---
title: Краткое руководство. Отправка поисковых запросов в REST API Поиск сущностей Bing с помощью C#
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как отправлять запросы в REST API Поиска сущностей Bing с помощью C# и получать ответы в формате JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 99f2197732a332e860af421f46d1049c2388043c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221236"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Краткое руководство. Отправка запросов для поиска в REST API Поиска сущностей Bing с помощью C#

Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска сущностей и просмотреть ответ в формате JSON. Это простое приложение C# отправляет запрос на поиск новостей к API и отображает ответ. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Это приложение написано на C#. Но API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.


## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2017 или более поздней версии](https://www.visualstudio.com/downloads/).
- Если вы работаете в Linux или MacOS, для выполнения инструкций из этого краткого руководства можете использовать [Visual Studio Code](https://code.visualstudio.com/) и [.NET Core.](/dotnet/core/install/macos)
- [Бесплатная учетная запись Azure.](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

1. Создайте консольное решение C# в Visual Studio. 
1. Добавьте пакет NuGet [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/).
    1. В **Обозревателе решений** щелкните проект правой кнопкой мыши.
    2. Выберите **Manage NuGet Packages...** (Управление пакетами NuGet...).
    3. Найдите и выберите пакет *Newtonsoft.Json* . Установите его.
1. Затем добавьте следующие пространства имен в основной файл кода:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Создайте класс и добавьте переменные для конечной точки API, ключ подписки и запрос для поиска. Вы можете использовать глобальную конечную точку, указанную в коде ниже, или конечную точку [личного поддомена](../../../cognitive-services/cognitive-services-custom-subdomains.md), которая отображается на портале Azure для вашего ресурса.

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

1. В классе создайте функцию с именем `Search()`. В этой функции создайте объект `HttpClient` и добавьте к заголовку ключ подписки `Ocp-Apim-Subscription-Key`.

2. Создайте универсальный код ресурса (URI) для вашего запроса, объединив хост и путь и добавив market. Кроме того, выполните URL-кодирование своего запроса.

3. Получите HTTP-ответ с помощью метода `client.GetAsync()`. Затем сохраните его в формате JSON с помощью метода `ReadAsStringAsync()`.

4. Отформатируйте строку JSON с помощью `JsonConvert.DeserializeObject()` и выведите ее в консоль.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. В методе `Main()` приложения вызовите функцию `Search()`.
    
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
