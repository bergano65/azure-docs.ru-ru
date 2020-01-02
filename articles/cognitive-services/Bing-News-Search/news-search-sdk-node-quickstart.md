---
title: Краткое руководство. Поиск новостей с помощью пакета SDK для Node.js — Поиск новостей Bing
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как искать новости с помощью пакета SDK Поиска новостей Bing для Node.js и обрабатывать ответы.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9bbad7685cf7d562189871bb5fa4a56d69287db4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383884"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>Краткое руководство. Поиск новостей с помощью пакета SDK Поиска новостей Bing для Node.js

Используйте это краткое руководство, чтобы начать поиск новостей с помощью пакета SDK Поиска новостей Bing для Node.js. Поскольку REST API Поиска новостей Bing совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Предварительные требования

* [Node.js](https://nodejs.org/en/)

Чтобы установить консольное приложение с помощью пакета SDK для API "Поиск новостей Bing", сделайте следующее:
1. Выполните команду `npm install ms-rest-azure` в своей среде разработки.
2. Выполните команду `npm install azure-cognitiveservices-newssearch` в своей среде разработки.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте экземпляр `CognitiveServicesCredentials`. Создайте переменные для ключа подписки и условие поиска.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. Создайте экземпляр клиента.
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Отправка поискового запроса

1. Используйте клиент, чтобы выполнить поиск по условию, например "Winter Olympics" как в этом примере.
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

Код выводит элементы `result.value` в консоль без анализа какого-либо текста. Если будут получены результаты для какой-либо категории, они будут включать в себя следующее:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-news-search-single-page-app.md)
