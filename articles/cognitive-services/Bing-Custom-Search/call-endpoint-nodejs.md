---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Node.js | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Узнайте, как запрашивать результаты поиска из экземпляра Пользовательского поиска Bing с помощью Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 43710407386995bde6d3505286e96b0737e06f08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91309791"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью Node.js

Узнайте, как запрашивать результаты поиска из экземпляра Пользовательского поиска Bing. Хотя это приложение написано на JavaScript, API Пользовательского поиска Bing представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". Дополнительные сведения см. в [кратком руководстве Создание первого экземпляра Пользовательского поиска Bing](quick-start.md).

- [Среда выполнения JavaScript Node.js.](https://www.nodejs.org/)

- [Библиотека запросов JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

- Создайте файл JavaScript в избранной интегрированной среде разработки или редакторе и добавьте оператор `require()` для библиотеки запросов. Создайте переменные для ключа подписки, идентификатора пользовательской конфигурации и условия поиска.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса 

1. Создайте переменную для хранения сведений, отправляемых в запросе. Создайте URL-адрес запроса, добавив условие поиска к параметру запроса `q=` и идентификатор пользовательской конфигурации экземпляра поиска к параметру `customconfig=`. Разделите параметры символом `&`. Вы можете использовать глобальную конечную точку в следующем коде или конечную точку [личного поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемую на портале Azure для вашего ресурса.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Используйте библиотеку запросов JavaScript для отправки поискового запроса к экземпляру Пользовательского поиска Bing и вывода на экран сведений о результатах, включая его имя, URL-адрес и дату последнего просмотра веб-страницы.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)
