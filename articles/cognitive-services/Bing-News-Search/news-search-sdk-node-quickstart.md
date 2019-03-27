---
title: Краткое руководство. Поиск новостей с помощью пакета SDK Поиска новостей Bing для Node.js
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как искать новости с помощью пакета SDK Поиска новостей Bing для Node.js и обрабатывать ответы.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 1930ef761b4be9d8085fd6e1785e78146c6b5547
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109174"
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
