---
title: 'Краткое руководство: транслитерация текста, Python — API перевода текстов'
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как с помощью API перевода текстов и Java преобразовать текст на одном языке из одного набора символов в Python.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 2621e3ae165efe9f592400e3ad2782b396cf2a60
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647454"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-python"></a>Краткое руководство: транслитерация текста с помощью REST API перевода текстов (Python)

Из этого краткого руководства вы узнаете, как с помощью API перевода текстов преобразовать текст на одном языке из одного набора символов в другой.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Python 3.x](https://www.python.org/downloads/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Запрос на транслитерацию

Приведенный ниже код преобразует текст на одном языке из одного набора символов в другой с помощью метода [Transliterate](./reference/v3-0-transliterate.md).

1. Создайте проект Python в любом удобном редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = '&language=ja&fromScript=jpan&toScript=latn';

# Transliterate "good afternoon".
text = 'こんにちは'

def transliterate (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = transliterate (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="transliterate-response"></a>Результат транслитерации

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, которые демонстрируют перевод и определение языка, а также с другими примерами проектов для API перевода текстов на сайте GitHub.

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
