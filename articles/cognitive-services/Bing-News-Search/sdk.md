---
title: Пакет SDK для Поиска новостей Bing
titleSuffix: Azure Cognitive Services
description: Пакет SDK для Поиска новостей Bing для приложений, которые выполняют поиск в Интернете.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801239"
---
# <a name="bing-search-sdk"></a>Пакет SDK для Поиска Bing
Примеры использования API Bing для поиска новостей включают в себя следующие сценарии:
1. Запрос новостей по условиям поиска с параметрами `market` и `count`, проверка количества результатов, а также вывод `totalEstimatedMatches`, имени, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
2. Запрос последних новостей по условиям поиска с параметрами `freshness` и `sortBy`, проверка количества результатов, а также вывод `totalEstimatedMatches`, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
3. Запрос новостей по категориям `movie` и `TV entertainment` с использованием безопасного поиска, проверка количества результатов, а также вывод категории, имени, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
4. Запрос новостей по набирающим популярность темам в Bing, проверка количества результатов и вывод имени, текста запроса, `webSearchUrl`, `newsSearchUrl` и URL-адреса изображения первого результата из списка новостей.

Пакеты SDK для службы "Поиск Bing" позволяют использовать функцию поиска в Интернете на указанных ниже языках программирования.
* Начало работы с [примерами для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
    * [Пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0).
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch).
* Начало работы с [примерами для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch).
* Начало работы с [примерами для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).
* Начало работы с [примерами для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch).

Примеры пакетов SDK для каждого языка содержат файл сведений, в котором указаны предварительные требования и инструкции по установке и запуску этих примеров.