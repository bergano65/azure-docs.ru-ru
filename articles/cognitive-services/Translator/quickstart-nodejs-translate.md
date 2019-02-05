---
title: Краткое руководство. Перевод текста, Node.js — API перевода текстов
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как перевести текст с одного языка на другой, используя API перевода текстов и Node.js.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 40b38fdf2773bd58cf1635d089aec7340b04ce07
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214028"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-with-nodejs"></a>Краткое руководство. Использование API перевода текстов для перевода строки с помощью Node.js

В этом кратком руководстве вы узнаете, как перевести строку с текстом с английского на итальянский и немецкий с помощью Node.js и REST API перевода текстов.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом API перевода текстов. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.


## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Node 8.12.x или более поздней версии](https://nodejs.org/en/);
* ключ подписки Azure для API перевода текстов.

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте проект, используя любую IDE или любой текстовый редактор. Затем скопируйте в файл проекта с именем `translate-text.js` этот фрагмент кода.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `npm install request uuidv4`.

Эти модули необходимы для построения HTTP-запроса и создания уникального идентификатора для заголовка `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Настройка ключа подписки

В этом фрагменте кода будет предпринята попытка считать ключ подписки API перевода текстов из переменной среды `TRANSLATOR_TEXT_KEY`. Если вы не знакомы с переменными среды, можно задать `subscriptionKey` в виде строки и закомментировать условный оператор.

Скопируйте в проект следующий код:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Настройка запроса

Метод `request()`, доступный через модуль запросов, позволяет передавать метод HTTP, URL-адрес, параметры запроса, заголовки и текст JSON как объект `options`. В этом фрагменте кода мы настроим запрос:

>[!NOTE]
> Дополнительные сведения о конечных точках, маршрутах и параметрах запросов см. в руководстве по [определениям в с помощью API перевода текстов 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': 'it',
      'to': 'de'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

Самый простой способ выполнить проверку подлинности запроса — это передать ключ подписки как заголовок `Ocp-Apim-Subscription-Key`, который мы используем в этом примере. В качестве альтернативы вы можете обменять свой ключ подписки на маркер доступа и передать маркер доступа в виде заголовка `Authorization` для проверки своего запроса. Дополнительные сведения см. в разделе [Authenticate to the Speech API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Аутентификация в API речи).

## <a name="make-the-request-and-print-the-response"></a>Выполнение запроса и вывод ответа

Теперь мы создадим запрос с помощью метода `request()`. Он принимает объект `options`, который мы создали в предыдущем разделе, в качестве первого аргумента, а затем выводит понятный ответ JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> В этом примере мы определяем HTTP-запрос в объекте `options`. Однако модуль запроса также поддерживает удобные методы, такие как `.post` и `.get`. См. дополнительные сведения [об удобных методах](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Сборка

Вот и все. Вы собрали простую программу, которая вызовет API перевода текстов и вернет ответ JSON. Теперь пришло время запустить ее.

```console
node translate-text.js
```

Если вы хотите сравнить свой код с нашей версией, см. [пример кода на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Пример ответа

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы закодировали свой ключ подписки в программе, обязательно удалите его после завершения работы с этим кратким руководством.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>См. также

Помимо определения языка, API перевода текстов можно использовать для выполнения и других задач, в частности:

* [транслитерация текста](quickstart-nodejs-transliterate.md);
* [определение языка по входным данным](quickstart-nodejs-detect.md);
* [получение вариантов перевода](quickstart-nodejs-dictionary.md);
* [получение списка поддерживаемых языков](quickstart-nodejs-languages.md);
* [определение длины предложения на основе входных данных](quickstart-nodejs-sentences.md).
