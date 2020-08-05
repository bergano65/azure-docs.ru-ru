---
title: Краткое руководство по использованию клиентской библиотеки Поиска видео Bing для JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 7cf28df4f009b017699c926d1ca54b7e5320a179
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404126"
---
Используйте это краткое руководство, чтобы начать поиск новостей с помощью клиентской библиотеки Поиска видео Bing для JavaScript. Поскольку REST API Поиска видео Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Он содержит больше аннотаций и функций.

## <a name="prerequisites"></a>Предварительные требования

- [Node.js](https://www.nodejs.org/)

Чтобы установить консольное приложение с помощью клиентской библиотеки для Поиска видео Bing, сделайте следующее.
* Выполните команду `npm install ms-rest-azure` в своей среде разработки.
* Выполните команду `npm install azure-cognitiveservices-videosearch` в своей среде разработки.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте оператор `require()` для клиентской библиотеки Поиска видео Bing и модуль `CognitiveServicesCredentials`. Создайте переменную для ключа подписки. 
    
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Одностраничное приложение для поиска видео](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также раздел 

* [Что такое API Bing для поиска видео?](../../overview.md)
* [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)