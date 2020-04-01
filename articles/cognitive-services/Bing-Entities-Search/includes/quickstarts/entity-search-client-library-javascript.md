---
title: Краткое руководство по использованию клиентской библиотеки Поиска сущностей Bing для JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: ac2f83c824014e16cfbe9ab18483b914ed8b077d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136610"
---
Используйте это краткое руководство, чтобы начать поиск сущностей с помощью клиентской библиотеки Поиска сущностей Bing для JavaScript. Поскольку REST API Поиска сущностей Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/en/download/).

* [Пакет SDK Поиска сущностей Bing для Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Чтобы установить пакет SDK Поиска сущностей Bing, сделайте следующее:

1. Выполните команду `npm install ms-rest-azure` в своей среде разработки.
2. Выполните команду `npm install @azure/cognitiveservices-entitysearch` в своей среде разработки.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте следующие требования.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../../overview.md)