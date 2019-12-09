---
title: Краткое руководство. Перечисление голосовых моделей для преобразований текста в речь с помощью Node.js — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как получить полный список стандартных и нейронных голосовых моделей для региона или конечной точки с использованием Node.js. Список возвращается как JSON, а доступность голосовой модели зависит от региона.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd063102a4c0cbdc4b62df027e643b117b412278
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812854"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Краткое руководство. Получение списка голосовых моделей для преобразований текста в речь с использованием Node.js

В этом кратком руководстве вы узнаете, как получить полный список стандартных и нейронных голосовых моделей для региона или конечной точки с использованием Node.js. Список возвращается как JSON, а доступность голосовой модели зависит от региона. Список поддерживаемых регионов см. в разделе [Регионы](regions.md).

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](get-started.md), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

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
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Если вы ранее не использовали эти модули, вам потребуется установить их перед запуском программы. Чтобы установить эти пакеты, выполните команду: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Получение маркера доступа

REST API преобразования текста в речь требует предоставить маркер доступа для аутентификации. Чтобы получить маркер доступа, нужно выполнить обмен. Эта функция меняет ключ подписки службы "Речь" на маркер доступа через конечную точку `issueToken`.

В этом примере предполагается, что подписка службы "Речь" размещена в регионе "Западная часть США". Если вы используете другой регион, измените значение `uri`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

В следующем разделе будет создана функция, которая получает список голосов и сохраняет в файл выходные данные JSON.

## <a name="make-a-request-and-save-the-response"></a>Выполнение запроса и сохранение ответа

Здесь вы создадите запрос и сохраните список возвращенных голосовых моделей. В этом примере предполагается, что вы используете конечную точку в регионе "Западная часть США". Если ресурс зарегистрирован в другом регионе, обновите значение `uri`. Дополнительные сведения см. в [списке регионов службы "Речь"](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Теперь следует добавить в запрос обязательные заголовки. И наконец, направьте запрос в службу. Если запрос выполнен успешно, возвращается код состояния 200 и ответ сохраняется как файл.

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

Осталось совсем немного. Последним шагом является создание асинхронной функции. Эта функция считает ключ подписки из переменной среды, получит маркер, подождет завершения запроса и выполнить запись в файл ответа JSON.

Если вы не знакомы с переменными среды или предпочитаете выполнять проверку с помощью ключа подписки, жестко заданного в качестве строки, замените `process.env.SPEECH_SERVICE_KEY` на ключ подписки в качестве строки.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>См. также

* [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
