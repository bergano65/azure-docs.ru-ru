---
title: Краткое руководство. API Bing для проверки орфографии, Ruby
titlesuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API Bing для проверки орфографии.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: e75b8e8b23bbb8e49cb8d8ec55e42f6ffb204197
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023504"
---
# <a name="quickstart-for-bing-spell-check-api-with-ruby"></a>Краткое руководство по API Bing для проверки орфографии с использованием Ruby 

В этой статье показано, как использовать [API Bing для проверки орфографии](https://azure.microsoft.com/services/cognitive-services/spell-check/)  с Ruby. API проверки орфографии возвращает список нераспознанных слов вмсте с предлагаемыми вариантами для замены. Обычно сначала текст отправляется в API, а затем выполняются предложенные замены в тексте либо список замен отображается для пользователя приложения, который решает, нужно ли делать замены. В этой статье показано, как отправить запрос, который содержит текст "Hollo, wrld!". Для замены предлагаются варианты "Hello" и "world".

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) или более поздней версии.

Вам потребуется [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API Bing для проверки орфографии версии 7**. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/#lang). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="get-spell-check-results"></a>Получение результатов проверки орфографии

1. Создайте новый проект Ruby в избранной интегрированной среде разработки.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```ruby
require 'net/http'
require 'uri'
require 'json'

uri = 'https://api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

uri = URI(uri + path + params)
uri.query = URI.encode_www_form({
    # Request parameters
    'text' => 'Hollo, wrld!'
})

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>
#
# See below for more information.

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/x-www-form-urlencoded"

request['Ocp-Apim-Subscription-Key'] = key

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

result = JSON.pretty_generate(JSON.parse(response.body))
puts result
```

**Ответ**

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
> [Руководство по API Bing для проверки орфографии](../tutorials/spellcheck.md)

## <a name="see-also"></a>См. также

- [Общие сведения об API Bing для проверки орфографии](../proof-text.md)
- [Справочник по API проверки орфографии Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
