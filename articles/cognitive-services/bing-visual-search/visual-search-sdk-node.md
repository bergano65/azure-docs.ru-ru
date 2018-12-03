---
title: 'Краткое руководство: пакет SDK визуального поиска Bing, Node'
titleSuffix: Azure Cognitive Services
description: Установка компонентов консольного приложения пакета SDK для визуального поиска для Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: cca39fb16c47eb13887f6198833dafc64974d301
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444466"
---
# <a name="quickstart-bing-visual-search-sdk-node"></a>Краткое руководство: пакет SDK визуального поиска Bing для Node 

Пакет SDK для визуального поиска Bing содержит функции REST API для обработки веб-запросов и анализа результатов.
[Исходный код примеров для пакета SDK для визуального поиска для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) доступен на сайте GitHub.

Сценарии кода описаны под приведенными далее заголовками.
* [Клиент визуального поиска](#client)
* [Готовое консольное приложение](#complete)

## <a name="prerequisites"></a>Предварительные требования

* Для выполнения действий, описанных в этом кратком руководстве, нужно создать подписку в ценовой категории S9, как указано на странице [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Чтобы создать подписку на портале Azure:
1. Введите запрос BingSearchV7 в строке поиска с текстом `Search resources, services, and docs` в верхней части страницы на портале Azure.  
2. В меню Marketplace в раскрывающемся списке выберите `Bing Search v7`.
3. В поле `Name` (Имя) введите имя нового ресурса.
4. Выберите подписку `Pay-As-You-Go` (Оплата по мере использования).
5. Выберите ценовую категорию `S9`.
6. Щелкните `Enable` (Активировать), чтобы создать подписку.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы установить консольное приложение с помощью пакета SDK для API "Визуальный поиск Bing", сделайте следующее:
* Запустите `npm install ms-rest-azure`
* Запустите `npm install azure-cognitiveservices-search-visualSearch`.

<a name="client"></a>
## <a name="visual-search-client"></a>Клиент визуального поиска
Чтобы создать экземпляр клиента `VisualSearchAPI`, добавьте директивы using:
```
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;

```
Затем создайте экземпляр клиента.
```
let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);

```
Используйте его для поиска изображений.
```
let fileStream = fs.createReadStream('../Data/image.jpg');
let visualSearchRequest = JSON.stringify({});
let visualSearchResults;
try {
    visualSearchResults = await visualSearchApiClient.images.visualSearch({
        image: fileStream,
        knowledgeRequest: visualSearchRequest
    });
    console.log("Search visual search request with binary of image");
} catch (err) {
    console.log("Encountered exception. " + err.message);
}

```
Проанализируйте результаты предыдущего запроса.
```
// Visual Search results
if (visualSearchResults.image.imageInsightsToken) {
    console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
}
else {
    console.log("Couldn't find image insights token!");
}

// List of tags
if (visualSearchResults.tags.length > 0) {
    let firstTagResult = visualSearchResults.tags[0];
    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

    // List of actions in first tag
    if (firstTagResult.actions.length > 0) {
        let firstActionResult = firstTagResult.actions[0];
        console.log(`First tag action count: ${firstTagResult.actions.length}`);
        console.log(`First tag action type: ${firstActionResult.actionType}`);
    }
    else {
        console.log("Couldn't find tag actions!");
    }

}
else {
    console.log("Couldn't find image tags!");
}

```
<a name="complete"></a>
## <a name="complete-console-application"></a>Готовое консольное приложение

Следующее консольное приложение выполняет предыдущий код, другие сценарии и анализирует результаты:
```
/*
 * Copyright (c) Microsoft Corporation. All rights reserved.
 * Licensed under the MIT License. See License.txt in the project root for
 * license information.
 */
'use strict';

const os = require("os");
const async = require('async');
const fs = require('fs');
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;


let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';

let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
let visualModels = visualSearchApiClient.models;
sample();

function sample() {
    async.series([
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let visualSearchRequest = JSON.stringify({});
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("Visual search request with binary of dog image");
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
            if (!visualSearchResults) {
                console.log("No visual search result data. ");
            } else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                else {
                    console.log("Couldn't find image insights token!");
                }

                // List of tags
                if (visualSearchResults.tags.length > 0) {
                    let firstTagResult = visualSearchResults.tags[0];
                    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                    // List of actions in first tag
                    if (firstTagResult.actions.length > 0) {
                        let firstActionResult = firstTagResult.actions[0];
                        console.log(`First tag action count: ${firstTagResult.actions.length}`);
                        console.log(`First tag action type: ${firstActionResult.actionType}`);
                    }
                    else {
                        console.log("Couldn't find tag actions!");
                    }

                }
                else {
                    console.log("Couldn't find image tags!");
                }
            }
        },
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with binary of image with JSON info of cropArea");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            let imageInfo = { url: "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80" };
            let filters = { site: "pinterest.com" };
            let knowledgeRequest = { filters: filters };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo, knowledgeRequest: knowledgeRequest });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nSearch visual search request with imageInfo and filters in JSON text");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            var imageInsightsToken = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D";
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { imageInsightsToken: imageInsightsToken, cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with ImageInsightsToken and crop area of image");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        function () {
            return new Promise((resolve) => {
                console.log(os.EOL);
                console.log("Finished running Visual-Search sample.");
                resolve();
            })
        }
    ], (err) => {
        throw (err);
        });
}

exports.sample = sample;
```

## <a name="next-steps"></a>Дополнительная информация

[Примеры пакета SDK для .NET Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
