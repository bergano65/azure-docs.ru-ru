---
title: Краткое руководство по использованию клиентских библиотек Поиска в Интернете Bing (JavaScript)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 20eaf6e6a9f0eee15e6ad6a5bd8f23bf8531545c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376496"
---
Клиентская библиотека Поиска в Интернете Bing позволяет интегрировать поиск Bing в любое приложение Node.js. Из этого краткого руководства вы узнаете, как создать экземпляр клиента, отправить запрос и вывести ответ.

Хотите увидеть код прямо сейчас? Примеры для клиентских библиотек [Поиска Bing для JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) доступны на сайте GitHub.

## <a name="prerequisites"></a>предварительные требования
Для работы с этим кратким руководством вам потребуется следующее:

* [Node.js 6](https://nodejs.org/en/download/) или более поздней версии;
* ключ подписки;  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Давайте начнем с настройки среды разработки для проекта Node.js.

1. Создайте каталог для своего проекта:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Создайте файл пакета:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Теперь установите несколько модулей Azure и добавьте их в `package.json`:

    ```console
    npm install --save @azure/cognitiveservices-websearch
    npm install --save @azure/ms-rest-azure-js
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Создание проекта и объявление обязательных модулей

В том же каталоге, где находится `package.json`, создайте проект Node.js с помощью любой среды IDE или редактора. Например: `sample.js`.

Скопируйте в приложение следующий код: Он загружает модули, установленные в предыдущем разделе.

```javascript
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const WebSearchAPIClient = require('@azure/cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Создание экземпляра клиента

Этот код создает экземпляр клиента с помощью модуля `@azure/cognitiveservices-websearch`. Не забудьте ввести действующий ключ подписки Azure, прежде чем продолжить работу.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Выполнение запроса и вывод результатов

Используйте клиент для отправки запроса в Поиск в Интернете Bing. Если ответ содержит результаты для любого из элементов массива `properties`, в консоль выводится `result.value`.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Запуск программы

А теперь запустите программу!

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ключ подписки из кода программы после завершения проекта.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры использования пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>См. также раздел

* [Справочник по пакету SDK Azure для Node](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
