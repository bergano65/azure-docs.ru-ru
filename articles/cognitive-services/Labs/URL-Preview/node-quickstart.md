---
title: Краткое руководство. API предварительного просмотра URL-адресов в проектах (Node.js)
titlesuffix: Azure Cognitive Services
description: Пример скрипта для начала работы со службой предварительного просмотра URL-адресов, входящей в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 3367b39333c9d7334bc56b3acdf00453cc9705c0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465055"
---
# <a name="quickstart-url-preview-with-nodejs"></a>Краткое руководство. API предварительного просмотра URL-адресов (Node.js) 

Следующий пример Node создает представление для предварительного просмотра URL-адреса веб-сайта SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

## <a name="code-scenario"></a>Сценарий кода 

Следующий код позволяет получить данные предварительного просмотра URL-адреса.
Он реализуется с помощью следующих действий:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Укажите URL-адрес запроса для предварительного просмотра и добавьте параметр запроса.  
3. Создайте функцию обработчика для ответа.
4. Определите функцию поиска, которая создает запрос и добавляет заголовок *Ocp-Apim-Subscription-Key*.
5. Запустите функцию поиска. 

Полный код этого примера выглядит так:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-ACCESS-KEY'; 
let host = 'api.labs.cognitive.microsoft.com';
let path = '/urlpreview/v7.0/search';

let mkt = 'en-US';
let q = 'https://swiftkey.com/';

let params = '?q=' + encodeURI(q);

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

````

## <a name="next-steps"></a>Дополнительная информация
- [Пример кода C#](csharp.md)
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — JavaScript](javascript.md)
- [Краткое руководство — Python](python-quickstart.md)