---
title: Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и C#
titleSuffix: Azure Cognitive Services
description: Приступите к работе с REST API проверки орфографии Bing для проверки орфографии и грамматики.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: a2c121ed58882427022b716081b096c913d447f8
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423631"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и C#

В этом кратком руководстве показано, как отправить первый вызов к REST API "Проверка орфографии Bing". Это простое приложение C# отправляет запрос к API и возвращает список предлагаемых исправлений. Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017 или более поздней версии](https://www.visualstudio.com/downloads/).
* Чтобы установить `Newtonsoft.Json` в качестве пакета NuGet в Visual Studio, сделайте следующее.
    1. Щелкните правой кнопкой мыши файл решения в **обозревателе решений**.
    1. Выберите **Управление пакетами NuGet для решения**.
    1. Затем выполните поиск пакета `Newtonsoft.Json` и установите его.
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

1. Создайте консольное решение `SpellCheckSample` в Visual Studio. Затем добавьте следующие пространства имен в основной файл кода.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Создайте переменные для конечной точки API, ключа подписки и текста, в котором нужно проверить орфографию.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Создайте переменную для параметров поиска. Добавьте код рынка после `mkt=`. Код рынка определяет страну, из которой выполняется запрос. Кроме того, добавьте режим проверки орфографии после `&mode=`. Можно указать режим `proof` (выявляет большинство орфографических и грамматических ошибок) или `spell` (выявляет большинство орфографических ошибок, но не так много грамматических ошибок).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Создание и отправка запроса на проверку орфографии

1. Создайте асинхронную функцию `SpellCheck()` для отправки запроса в API. Создайте `HttpClient` и добавьте ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. Затем выполните следующие шаги в рамках функции.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Создайте URI для запроса путем, добавив узел, путь и параметры.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Создайте список с объектом `KeyValuePair`, в котором содержится текст, и используйте его для создания объекта `FormUrlEncodedContent`. Задайте данные заголовка и используйте `PostAsync()` для отправки запроса.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>Получение и вывод ответа API

### <a name="get-the-client-id-header"></a>Получение заголовка идентификатора клиента

Если в ответе содержится заголовок `X-MSEdge-ClientID`, получите значение и выведите его.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Получение ответа

Получите ответ от API. Десериализируйте объект JSON и выведите его в консоли.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Вызов функции проверки орфографии

В функции Main проекта вызовите `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Пример ответа в формате JSON

Успешный ответ возвращается в формате JSON, как показано в примере ниже. 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../tutorials/spellcheck.md)

- [Что такое API проверки орфографии Bing?](../overview.md)
- [Справочник по API Проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
