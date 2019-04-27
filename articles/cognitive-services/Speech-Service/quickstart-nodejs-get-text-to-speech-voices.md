---
title: Краткое руководство. Список преобразования текста в речь голоса, Node.js — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как получить полный список "стандартный" и нейронной голоса для региона или конечной точки с помощью Node.js. Список возвращается в виде JSON и доступность голоса зависит от региона.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620266"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Краткое руководство. Получите список преобразования текста в речь голоса с помощью Node.js

В этом кратком руководстве вы узнаете, как получить полный список "стандартный" и нейронной голоса для региона или конечной точки с помощью Node.js. Список возвращается в виде JSON и доступность голоса зависит от региона. Список поддерживаемых регионов см. в разделе [регионах](regions.md).

В этом кратком руководстве требуется [учетной записи Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом служб речи. Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Технические условия

Для работы с этим кратким руководством вам понадобится:

* [Node 8.12.x или более поздней версии](https://nodejs.org/en/);
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Создание проекта и объявление зависимостей

Создайте проект Node.js в используемой вами интегрированной среде разработки или редакторе. Затем скопируйте в файл проекта с именем `get-voices.js` этот фрагмент кода.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `npm install request request-promise`.

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

В следующем разделе мы создадим функцию для получения списка голоса и сохранить выходные данные JSON в файл.

## <a name="make-a-request-and-save-the-response"></a>Выполнение запроса и сохранение ответа

Здесь вы собираетесь создать запрос и сохранить список возвращенных голоса. В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `uri`. Дополнительные сведения см. в разделе [области служб речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Затем добавьте необходимые заголовки для запроса. И наконец, направьте запрос в службу. Если запрос выполнен успешно, и возвращается код состояния 200, ответ записывается в файл.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Сборка

Осталось совсем немного. Последним шагом является создание асинхронной функции. Эта функция будет считывать ключ подписки из переменной среды, получить маркер, дождитесь завершения запроса, а затем поток ответа JSON к файлу.

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
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Теперь все готово к запуску примера приложения. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>См. также

* [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
