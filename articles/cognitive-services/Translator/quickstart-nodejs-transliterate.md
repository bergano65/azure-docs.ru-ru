---
title: Краткое руководство. Транслитерация текста с помощью Node.js и API перевода текстов
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как транслитерировать (преобразовывать) текст из одного письма в другое с помощью Node.js и REST API перевода текстов. В этом примере японский текст транслитерируется в текст на латинице.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c7b5a75f9c73ef470ebb84a8b42f7400c81f0b96
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444969"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-with-nodejs"></a>Краткое руководство. Транслитерация текста с помощью Node.js и API перевода текстов

Из этого краткого руководства вы узнаете, как транслитерировать (преобразовывать) текст из одного письма в другое с помощью Node.js и REST API перевода текстов. В приведенном примере японский текст транслитерируется в текст на латинице.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом API перевода текстов. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Node 8.12.x или более поздней версии](https://nodejs.org/en/);
* ключ подписки Azure для API перевода текстов.

## <a name="create-a-project-and-import-required-modules"></a>Создание проекта и импорт обязательных модулей

Создайте новый проект в предпочитаемой интегрированной среде разработки или текстовом редакторе или создайте папку `translate-text.js` на рабочем столе. Затем скопируйте в проект/файл следующий фрагмент кода.

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
> Дополнительные сведения о конечных точках, маршрутах и параметрах запросов см. в руководстве по [определениям в транслитерация](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'transliterate',
    qs: {
      'api-version': '3.0',
      'language': 'ja',
      'fromScript': 'jpan',
      'toScript': 'latn'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'こんにちは'
    }],
    json: true,
};
```

Самый простой способ выполнить проверку подлинности запроса — это передать ключ подписки как заголовок `Ocp-Apim-Subscription-Key`, который мы используем в этом примере. В качестве альтернативы вы можете обменять свой ключ подписки на маркер доступа и передать маркер доступа в виде заголовка `Authorization` для проверки своего запроса. 

Если вы используете подписку на несколько служб Cognitive Services, необходимо также включить `Ocp-Apim-Subscription-Region` в заголовках запроса. 

Дополнительные сведения см. в разделе [Authenticate to the Speech API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) (Аутентификация в API речи).

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
node transliterate-text.js
```

Если вы хотите сравнить свой код с нашей версией, см. [пример кода на сайте GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Пример ответа

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
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

* [перевод текста](quickstart-nodejs-translate.md);
* [определение языка по входным данным](quickstart-nodejs-detect.md);
* [получение вариантов перевода](quickstart-nodejs-dictionary.md);
* [получение списка поддерживаемых языков](quickstart-nodejs-languages.md);
* [определение длины предложения на основе входных данных](quickstart-nodejs-sentences.md).
