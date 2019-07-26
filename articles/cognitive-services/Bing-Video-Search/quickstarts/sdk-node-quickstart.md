---
title: Краткое руководство. Поиск видео с помощью пакета SDK Поиска видео Bing для Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как отправлять запросы с помощью пакета SDK Поиска видео Bing для Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: aahi
ms.openlocfilehash: 12eafca9c673d95813eefcd58d2b3f9ba7b54fd3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358886"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>Краткое руководство. Поиск видео с помощью пакета SDK Поиска видео Bing для Node.js

Используйте это краткое руководство, чтобы начать поиск новостей с помощью пакета SDK Поиска видео Bing для Node.js. Пока для Поиска видео Bing имеется REST API, совместимый с большинством языков, пакет SDK предоставляет простой способ интегрировать службу в приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Он содержит больше аннотаций и функций.

## <a name="prerequisites"></a>Предварительные требования

- [Node.js](https://www.nodejs.org/)

Чтобы установить консольное приложение с помощью пакета SDK для API "Поиск видео Bing", сделайте следующее:
* Выполните команду `npm install ms-rest-azure` в своей среде разработки.
* Выполните команду `npm install azure-cognitiveservices-videosearch` в своей среде разработки.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте оператор `require()` для пакета SDK Поиска видео Bing и модуль `CognitiveServicesCredentials`. Создайте переменную для ключа подписки. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. Создайте экземпляр `CognitiveServicesCredentials` с помощью ключа. Затем используйте его для создания экземпляра клиента для поиска видео.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>Отправка поискового запроса

1. Используйте `client.videosOperations.search()` для отправки поискового запроса к API Поиска видео Bing. При возврате результатов поиска используйте `.then()` для записи результата в журнал.
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Одностраничное приложение для поиска видео](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также 

* [Что такое API Bing для поиска видео?](../overview.md)
* [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)