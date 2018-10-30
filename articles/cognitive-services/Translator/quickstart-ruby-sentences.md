---
title: Краткое руководство. Получение длины предложений, Ruby — API перевода текстов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве объясняется, как определять длину предложений в тексте с помощью API перевода текстов и Ruby.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: erhopf
ms.openlocfilehash: 18ef4e20c28b783738d2be929e063e6dada412d4
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644714"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-ruby"></a>Краткое руководство. Получение длины предложений с помощью REST API перевода текстов (Ruby)

В этом кратком руководстве объясняется, как определять длину предложений в тексте с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Ruby версии 2.4](https://www.ruby-lang.org/en/downloads/) или более поздней версии.

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Запрос метода BreakSentence

Приведенный ниже код позволяет разбить исходный текст на предложения с помощью метода [BreakSentence](./reference/v3-0-break-sentence.md).

1. Создайте новый проект Ruby в используемом редакторе.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'

uri = URI (host + path)

text = 'How are you? I am fine. What did you do today?'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="breaksentence-response"></a>Ответ метода BreakSentence

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Примеры на Ruby на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)
