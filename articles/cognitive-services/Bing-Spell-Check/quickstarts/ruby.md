---
title: Краткое руководство. Проверка орфографии с помощью REST API Проверки орфографии Bing и Ruby
titlesuffix: Azure Cognitive Services
description: Приступите к работе с REST API проверки орфографии Bing для проверки орфографии и грамматики.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a3e65f9ad8e8a9c6876d1588ecaa94531206c6f4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389705"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Краткое руководство. Проверка орфографии с помощью REST API Проверки орфографии Bing и Ruby

В этом кратком руководстве показано, как с помощью Ruby отправить вызов к REST API Проверки орфографии Bing. Это простое приложение отправляет запрос к API и возвращает список слов, которые не удалось распознать, с предлагаемыми исправлениями. Хотя это приложение создается на языке Ruby, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb).

## <a name="prerequisites"></a>Предварительные требования

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) или более поздней версии.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл Ruby в выбранном редакторе или интегрированной среде разработки и добавьте следующие требования. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Создайте переменные для вашего ключа подписки, универсальный код ресурса (URI) и путь для конечной точки. Создайте параметры запроса, добавив параметр `mkt=` для языкового стандарта и `&mode` для режима проверки `proof`.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Отправка запроса проверки орфографии

1. Создайте объект URI, указав универсальный код ресурса, путь и строку параметров узла. Передайте в запросе текст, для которого необходимо проверить орфографию.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Создайте запрос с помощью URI, созданного выше. Добавьте ключ в заголовок запроса `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Отправьте запрос.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Получите ответ в JSON и выведите его в консоль. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
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
- [Справочник по API проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
