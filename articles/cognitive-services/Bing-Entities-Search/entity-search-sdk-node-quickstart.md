---
title: Краткое руководство по работе с пакетом SDK для API Bing для поиска сущностей, Node
titleSuffix: Azure Cognitive Services
description: Настройка консольного приложения, созданного с помощью пакета SDK для API для поиска сущностей, с использованием Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 69bcca7871d9bf4bebf64c0c0ae1b54cd8408927
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684126"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Краткое руководство по работе с пакетом SDK для API Bing для поиска сущностей с использованием Node

Пакет SDK Bing для поиска сущностей содержит функции REST API для обработки запросов сущностей и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска сущностей для C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) доступен на сайте GitHub.
## <a name="application-dependencies"></a>Зависимости приложения

Чтобы установить консольное приложение с помощью пакета SDK для API "Поиск сущностей Bing", сделайте следующее:
* Выполните команду `npm install ms-rest-azure` в своей среде разработки.
* Выполните команду `npm install azure-cognitiveservices-entitysearch` в своей среде разработки.

## <a name="entity-search-client"></a>Клиент Поиска сущностей
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Создайте экземпляр `CognitiveServicesCredentials`.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента и выполните поиск результатов.
```
const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');

let entitySearchApiClient = new EntitySearchAPIClient(credentials);

entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
    console.log(result.queryContext);
    console.log(result.entities.value);
    console.log(result.entities.value[0].description);
}).catch((err) => {
    throw err;
});

```
Код выводит элементы `result.value` в консоль без анализа какого-либо текста.  Если будут получены результаты для какой-либо категории, они будут включать в себя следующее:
- _type: 'Thing'
- _type: 'ImageObject'

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)