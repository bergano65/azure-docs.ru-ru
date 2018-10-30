---
title: Краткое руководство. Транслитерация текста, Node.js — API перевода текстов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как с помощью API перевода текстов и Java преобразовать текст на одном языке из одного набора символов в Node.js.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 1980adb78a4ba457fd05f532cdd6e30bba7d9132
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646202"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-nodejs"></a>Краткое руководство. Транслитерация текста с помощью REST API перевода текстов (Node.js)

Из этого краткого руководства вы узнаете, как с помощью API перевода текстов преобразовать текст на одном языке из одного набора символов в другой.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Node.js 6](https://nodejs.org/en/download/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Запрос на транслитерацию

Приведенный ниже код преобразует текст на одном языке из одного набора символов в другой с помощью метода [Transliterate](./reference/v3-0-transliterate.md).

1. Создайте проект Node.js в любом редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/transliterate?api-version=3.0';

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
let params = '&language=ja&fromScript=jpan&toScript=latn';

// Transliterate "good afternoon".
let text = 'こんにちは';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Transliterate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Transliterate (content);
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
> [Примеры для Node.js на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
