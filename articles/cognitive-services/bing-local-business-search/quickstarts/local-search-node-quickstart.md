---
title: Краткое руководство. Отправка запроса в API Поиска местных компаний Bing с помощью Node.js
titleSuffix: Azure Cognitive Services
description: С помощью этого краткого руководства вы сможете начать отправку запросов в Bing Local Business Search API в Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: d0ee55c0baa6d7ec2ff86aa0e37b10ca4c973b77
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408141"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Node.js

Из этого краткого руководства вы узнаете, как отправлять запросы в API Поиска местных компаний Bing в Azure Cognitive Services. Хотя это простое приложение написано на Node.js, API представляет собой веб-службу на основе REST, совместимую с любым языком программирования, который поддерживает выполнение HTTP-запросов и анализ JSON.

В этом примере приложения из API извлекаются сведения о местных компаниях по поисковому запросу.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Последняя версия [Node.js](https://nodejs.org/en/download/).
* [Библиотека запросов JavaScript.](https://github.com/request/request)
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="создайте ресурс Поиска Bing"  target="_blank">Create a Bing Search resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.


## <a name="code-scenario"></a>Сценарий кода

Приведенный ниже код определяет и отправляет запрос. Этот код реализуется с помощью следующих действий:

1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Укажите запрос и добавьте параметр запроса.
3. Создайте функцию обработчика для ответа.
4. Определите функцию поиска, которая создает запрос и добавляет заголовок `Ocp-Apim-Subscription-Key`.
5. Запустите функцию поиска.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Поиск местных компаний с помощью C#](local-quickstart.md)
* [Краткое руководство. Отправка запроса в API Bing для поиска местных компаний с помощью Java](local-search-java-quickstart.md)
* [Краткое руководство. Отправка запроса в API Bing Local Business Search с помощью Python](local-search-python-quickstart.md)
