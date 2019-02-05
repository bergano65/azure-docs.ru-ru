---
title: Краткое руководство. Определение языка текста с помощью API перевода текстов и Ruby
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как определить язык исходного текста, используя API перевода текстов и Ruby.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: erhopf
ms.openlocfilehash: d710f1ae1df04cf5e97a3583a0cbe9a7c59abdb3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461140"
---
# <a name="quickstart-identify-language-from-text-with-the-translator-text-rest-api-ruby"></a>Краткое руководство. Определение языка текста с помощью REST API перевода текстов (Ruby)

Из этого краткого руководства вы узнаете, как определить язык исходного текста, используя API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Ruby версии 2.4](https://www.ruby-lang.org/en/downloads/) или более поздней версии.

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="detect-request"></a>Запрос метода Detect

В приведенном ниже коде язык исходного текста определяется с помощью метода [Detect](./reference/v3-0-detect.md).

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
path = '/detect?api-version=3.0'

uri = URI (host + path)

text = 'Salve, mondo!'

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

## <a name="detect-response"></a>Ответ метода Detect

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Примеры на Ruby на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)
