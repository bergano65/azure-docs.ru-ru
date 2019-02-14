---
title: Краткое руководство. API Bing для проверки орфографии, Python
titlesuffix: Azure Cognitive Services
description: Информация и примеры кода, которые помогут вам приступить к работе с API Bing для проверки орфографии.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: 5aa67c0e582d64f258da7abd01a9492daaf91efd
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882290"
---
# <a name="quickstart-for-bing-spell-check-api-with-python"></a>Краткое руководство по API Bing для проверки орфографии с использованием Python 

В этой статье показано, как использовать [API Bing для проверки орфографии](https://azure.microsoft.com/services/cognitive-services/spell-check/) с Python. API проверки орфографии возвращает список нераспознанных слов вмсте с предлагаемыми вариантами для замены. Обычно сначала текст отправляется в API, а затем выполняются предложенные замены в тексте либо список замен отображается для пользователя приложения, который решает, нужно ли делать замены. В этой статье показано, как отправить запрос, который содержит текст "Hollo, wrld!". Для замены предлагаются варианты "Hello" и "world".

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Python 3.x](https://www.python.org/downloads/).

Вам потребуется [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API Bing для проверки орфографии версии 7**. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/#lang). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure. См. также [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Получение результатов проверки орфографии

1. Создайте проект Python в используемой вами интегрированной среде разработки.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```python
import http.client, urllib.parse, json

text = 'Hollo, wrld!'

data = {'text': text}

# NOTE: Replace this example key with a valid subscription key.
key = 'ENTER KEY HERE'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/spellcheck?'
params = 'mkt=en-us&mode=proof'

headers = {'Ocp-Apim-Subscription-Key': key,
'Content-Type': 'application/x-www-form-urlencoded'}

# The headers in the following example 
# are optional but should be considered as required:
#
# X-MSEdge-ClientIP: 999.999.999.999  
# X-Search-Location: lat: +90.0000000000000;long: 00.0000000000000;re:100.000000000000
# X-MSEdge-ClientID: <Client ID from Previous Response Goes Here>

conn = http.client.HTTPSConnection(host)
body = urllib.parse.urlencode (data)
conn.request ("POST", path + params, body, headers)
response = conn.getresponse ()
output = json.dumps(json.loads(response.read()), indent=4)
print (output)
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
