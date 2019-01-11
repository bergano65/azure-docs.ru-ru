---
title: Краткое руководство. Преобразование текста в речь на Node.js — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как преобразовать текст в речь с помощью Node.js и REST API преобразования текста в речь. В это руководство включен пример текста, оформленный по стандарту разметки SSML (Speech Synthesis Markup Language). Этот стандарт позволяет выбрать голос и язык для речевого ответа.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: e2319262b669cd5a03fa3e50bf3e534e7974c72d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550626"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Краткое руководство. Преобразование текста в речь с использованием Node.js

В этом кратком руководстве описано, как преобразовать текст в речь с помощью Node.js и REST API преобразования текста в речь. Текст запроса в этом руководстве оформлен по стандарту разметки [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), который позволяет выбрать голос и язык для ответа.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Node 8.12.x или более поздней версии](https://nodejs.org/en/);
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Создание проекта и объявление зависимостей

Создайте проект Node.js в используемой вами интегрированной среде разработки или редакторе. Затем скопируйте в файл проекта с именем `tts.js` этот фрагмент кода.

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');

```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `npm install request readline-sync`.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Настройка ключа подписки и создание запроса для преобразования текста в речь

В следующих разделах описано, как создать функции для выполнения проверки подлинности, вызова API преобразования текста в речь и проверки ответа. Давайте начнем с добавления ключа подписки и создания приглашения для ввода текста.

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>Получение маркера доступа

REST API преобразования текста в речь требует предоставить маркер доступа для аутентификации. Чтобы получить маркер доступа, нужно выполнить обмен. Этот пример меняет ключ подписки службы "Речь" на маркер доступа через конечную точку `issueToken`.

Эта функция принимает два аргумента: ключ подписки службы "Речь" и функцию обратного вызова. После получения маркера доступа функция передает его функции обратного вызова. В следующем разделе мы создадим функцию для вызова API преобразования текста в речь и сохраним ответ в виде синтезированной речи.

В этом примере предполагается, что подписка службы "Речь" размещена в регионе "Западная часть США". Если вы используете другой регион, измените значение `uri`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Скопируйте в проект следующий код:

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> Дополнительные сведения о проверке подлинности см. в разделе [Проверка подлинности с помощью маркера доступа](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Выполнение запроса и сохранение ответа

Здесь вы создадите запрос к API преобразования текста в речь и сохраните ответ службы "Речь". В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `uri`. Дополнительные сведения см. в [списке регионов службы "Речь"](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Теперь следует добавить в запрос обязательные заголовки. Обязательно сохраните в `User-Agent` имя ресурса (размещенного на портале Azure), а в `X-Microsoft-OutputFormat` укажите нужный аудиовыход. См. [полный список форматов аудиовыхода](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Затем создайте текст запроса с соблюдением синтаксиса SSML. Этот пример определяет структуру и применяет входные данные `text`, которые вы создали ранее.

>[!NOTE]
> В этом примере используется голос `JessaRUS`. См. [полный список предоставляемых корпорацией Майкрософт голосов и языков](language-support.md).
> Если вы хотите создать для своего бренда уникальный и узнаваемый голос, см. руководство по [созданию пользовательского голоса](how-to-customize-voice-font.md).

И наконец, направьте запрос в службу. Если запрос выполнен успешно, возвращается код состояния 200 и речевой ответ сохраняется как `sample.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: '<speak version=\'1.0\' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang=\'en-US\'>\n<voice  name=\'Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)\'>' + text + '</voice> </speak>'
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>Сборка

Осталось совсем немного. Последний этап — вызов функции `textToSpeech`.

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Итак, все готово к запуску примера приложения преобразования текста в речь. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
node tts.js
```

Когда появится запрос, введите любой текст для преобразования в речь. Если все пройдет успешно, готовый речевой файл будет размещен в папке проекта. Воспроизведите его в любом проигрывателе мультимедиа.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>См. также

* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
