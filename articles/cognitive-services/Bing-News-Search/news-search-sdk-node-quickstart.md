---
title: Краткое руководство по работе с пакетом SDK для API Bing для поиска новостей, Node
titleSuffix: Azure Cognitive Services
description: Настройка консольного приложения, созданного с помощью пакета SDK для API Bing для поиска новостей.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: bc168cf696d6280ce4c0e7cb46f90af4a2ad7aa0
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686501"
---
# <a name="quickstart-bing-news-search-sdk-with-node"></a>Краткое руководство по работе с пакетом SDK для API Bing для поиска новостей с использованием Node

Пакет SDK для API Bing для поиска новостей содержит функции REST API для обработки запросов новостей и анализа результатов. 

[Исходный код примеров для пакета SDK для Поиска новостей Bing для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы установить консольное приложение с помощью пакета SDK для API "Поиск новостей Bing", сделайте следующее:
* Выполните команду `npm install ms-rest-azure` в своей среде разработки.
* Выполните команду `npm install azure-cognitiveservices-newssearch` в своей среде разработки.

## <a name="news-search-client"></a>Клиент для API "Поиск новостей"
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) с помощью *поиска*. Создайте экземпляр `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Используйте клиент, чтобы выполнить поиск по тексту запроса, как, например, по "Winter Olympics" в следующем примере:
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Код выводит элементы `result.value` в консоль без анализа какого-либо текста. Если будут получены результаты для какой-либо категории, они будут включать в себя следующее:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
