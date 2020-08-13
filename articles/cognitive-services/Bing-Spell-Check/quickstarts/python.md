---
title: Краткое руководство. Проверка орфографии с помощью REST API и Python — Проверка орфографии Bing
titleSuffix: Azure Cognitive Services
description: Приступите к работе с REST API Проверки орфографии Bing для проверки орфографии и грамматики при помощи этого руководства.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: e121aaf6725c179189b25dff6534b019c5de730c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852741"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Краткое руководство. Проверка орфографии с помощью REST API проверки орфографии Bing и Python

В этом кратком руководстве показано, как отправить первый вызов к REST API "Проверка орфографии Bing". Это простое приложение Python отправляет запрос к API и возвращает список предлагаемых исправлений. 

Это приложение создано на языке Python. Но API представляет собой веб-службу на основе REST, совместимую с большинством языков программирования. Исходный код этого приложения доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

## <a name="prerequisites"></a>Предварительные требования

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Инициализация приложения

1. Создайте файл Python в любой интегрированной среде разработки или редакторе и добавьте следующие инструкции импорта:

   ```python
   import requests
   import json
   ```

2. Создайте переменные для текста, орфографию которого необходимо проверить, ключ подписки и конечную точку Проверки орфографии Bing. Вы можете использовать глобальную конечную точку, указанную в коде ниже, или конечную точку [личного поддомена](../../../cognitive-services/cognitive-services-custom-subdomains.md), которая отображается на портале Azure для вашего ресурса.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Создание параметров для запроса

1. Создайте словарь, указав `text` как ключ и текст, который необходимо проверить, как значение.

    ```python
    data = {'text': example_text}
    ```

2. Добавьте к запросу параметры: 

   1. Назначьте код рынка для параметра `mkt` с помощью оператора `=`. Код рынка — это код страны или региона, из которого выполняется запрос. 

   1. Добавьте параметр `mode` с оператором `&` и назначьте режим проверки орфографии. Можно указать режим `proof` (выявляет большинство орфографических и грамматических ошибок) или `spell` (выявляет большинство орфографических ошибок, но не так много грамматических ошибок). 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Добавьте заголовок `Content-Type` и ключ подписки в заголовок `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Отправка запроса и считывание ответа

1. Отправьте запрос POST с помощью библиотеки запросов.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Получите ответ в формате JSON и выведите его.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Выполнение приложения

При использовании командной строки выполните приведенные ниже команды для запуска приложения:

```bash
python <FILE_NAME>.py
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../tutorials/spellcheck.md)

- [Что такое API проверки орфографии Bing?](../overview.md)
- [Справочник по API "Проверка орфографии Bing" версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
