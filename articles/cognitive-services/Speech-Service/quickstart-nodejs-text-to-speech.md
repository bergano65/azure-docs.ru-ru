---
title: Краткое руководство. Преобразование текста в речь на Node.js — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как преобразовать текст в речь с помощью Node.js и REST API преобразования текста в речь. В это руководство включен пример текста, оформленный по стандарту разметки SSML (Speech Synthesis Markup Language). Этот стандарт позволяет выбрать голос и язык для речевого ответа.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a7713576565ca2632d7d91857040ece4d02c411b
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520842"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Краткое руководство. Преобразование текста в речь с использованием Node.js

В этом кратком руководстве описано, как преобразовать текст в речь с помощью Node.js и REST API преобразования текста в речь. Текст запроса в этом руководстве оформлен по стандарту разметки [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), который позволяет выбрать голос и язык для ответа.

В этом кратком руководстве требуется [учетной записи Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом служб речи. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Технические условия

Для работы с этим кратким руководством вам понадобится:

* [Node 8.12.x или более поздней версии](https://nodejs.org/en/);
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Создание проекта и объявление зависимостей

Создайте проект Node.js в используемой вами интегрированной среде разработки или редакторе. Затем скопируйте в файл проекта с именем `tts.js` этот фрагмент кода.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Получение маркера доступа

REST API преобразования текста в речь требует предоставить маркер доступа для аутентификации. Чтобы получить маркер доступа, нужно выполнить обмен. Эта функция меняет местами ключ подписки служб речи для маркера доступа с помощью `issueToken` конечной точки.

В этом примере предполагается, что подписку служб речи находится в западной части США. Если вы используете другой регион, измените значение `uri`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Скопируйте в проект следующий код:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Дополнительные сведения о проверке подлинности см. в разделе [Проверка подлинности с помощью маркера доступа](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

В следующем разделе мы создадим функцию для вызова API преобразования текста в речь и сохраним ответ в виде синтезированной речи.

## <a name="make-a-request-and-save-the-response"></a>Выполнение запроса и сохранение ответа

Здесь вы создадите запрос к API преобразования текста в речь и сохраните ответ службы "Речь". В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `uri`. Дополнительные сведения см. в разделе [области служб речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Теперь следует добавить в запрос обязательные заголовки. Обязательно сохраните в `User-Agent` имя ресурса (размещенного на портале Azure), а в `X-Microsoft-OutputFormat` укажите нужный аудиовыход. См. [полный список форматов аудиовыхода](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Затем создайте текст запроса с соблюдением синтаксиса SSML. Этот пример определяет структуру и применяет входные данные `text`, которые вы создали ранее.

>[!NOTE]
> В этом примере используется голос `JessaRUS`. См. [полный список предоставляемых корпорацией Майкрософт голосов и языков](language-support.md).
> Если вы хотите создать для своего бренда уникальный и узнаваемый голос, см. руководство по [созданию пользовательского голоса](how-to-customize-voice-font.md).

И наконец, направьте запрос в службу. Если запрос выполнен успешно, возвращается код состояния 200 и речевой ответ сохраняется как `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

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
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Сборка

Осталось совсем немного. Последним шагом является создание асинхронной функции. Эта функция будет считывать ключ подписки из переменной среды, запрашивать текста, получить маркер, дождитесь запроса для завершения, а затем преобразовать текст в речь и сохранить звукового файла .wav.

Если вы еще не работали с переменными среды или использовать для тестирования с помощью вашей подписки ключа жестко задано в качестве строки, замените `process.env.SPEECH_SERVICE_KEY` с ключ подписки в виде строки.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Итак, все готово к запуску примера приложения преобразования текста в речь. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
node tts.js
```

Когда появится запрос, введите любой текст для преобразования в речь. Если все пройдет успешно, готовый речевой файл будет размещен в папке проекта. Воспроизведите его в любом проигрывателе мультимедиа.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>См. также

* [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
