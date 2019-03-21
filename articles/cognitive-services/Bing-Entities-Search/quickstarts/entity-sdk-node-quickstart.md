---
title: Краткое руководство. Отправка запросов для поиска в пакете SDK Поиска сущностей Bing для Node.js
titleSuffix: Azure Cognitive Services
description: Используйте это краткое руководство для поиска сущностей с помощью пакета SDK Поиска сущностей Bing для Node.js.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 015a2d344b066bd7b65c3228a2795c3395793f2b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097036"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>Краткое руководство. Отправка запросов для поиска в пакете SDK Поиска сущностей Bing для Node.js

Используйте это краткое руководство, чтобы начать поиск сущностей с помощью пакета SDK Поиска сущностей Bing для Node.js. Так как для Поиска сущностей Bing имеется REST API, совместимый с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/en/download/).

* [Пакет SDK Поиска сущностей Bing для Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Чтобы установить пакет SDK Поиска сущностей Bing, сделайте следующее:

1. Выполните команду `npm install ms-rest-azure` в своей среде разработки.
2. Выполните команду `npm install azure-cognitiveservices-entitysearch` в своей среде разработки.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте следующие требования. 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. Создайте экземпляр `CognitiveServicesCredentials` с помощью ключа подписки. Затем создайте с его помощью экземпляр клиента для поиска.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Отправка запроса и получение ответа

1. Отправьте запрос на поиск сущности с помощью `entitiesOperations.search()`. Получив ответ, выведите `queryContext`, число возвращенных результатов и описание первого результата.
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../overview.md )