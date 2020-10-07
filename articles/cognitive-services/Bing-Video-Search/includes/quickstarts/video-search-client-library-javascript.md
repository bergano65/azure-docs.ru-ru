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
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377108"
---
Используйте это краткое руководство, чтобы начать поиск новостей с помощью клиентской библиотеки Поиска видео Bing для JavaScript. Поскольку REST API Поиска видео Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js). Он содержит больше аннотаций и функций.

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/en/download/).
* [Пакет SDK для Поиска видео Bing для JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch).
     *  Чтобы установить его, выполните такую команду. `npm install @azure/cognitiveservices-videosearch`
* Класс `CognitiveServicesCredentials` из пакета `@azure/ms-rest-azure-js` для аутентификации клиента.
     * Чтобы установить его, выполните такую команду. `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте оператор `require()` для клиентской библиотеки Поиска видео Bing и модуль `CognitiveServicesCredentials`. Создайте переменную для ключа подписки. 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
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