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
ms.openlocfilehash: 1f2a5f6a1473cde40928ada6e30f6bd9b780543d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814888"
---
# <a name="quickstart-bing-entity-search-sdk-with-node"></a>Краткое руководство по работе с пакетом SDK для API Bing для поиска сущностей с использованием Node

Пакет SDK Bing для поиска сущностей содержит функции REST API для обработки запросов сущностей и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска сущностей для C#](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) доступен на сайте GitHub.
## <a name="application-dependencies"></a>Установка зависимостей

Чтобы установить консольное приложение, использующее пакет SDK Bing для поиска сущностей, выполните команду `npm install azure-cognitiveservices-entitysearch` в среде разработки.

## <a name="entity-search-client"></a>Клиент API для поиска сущностей
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