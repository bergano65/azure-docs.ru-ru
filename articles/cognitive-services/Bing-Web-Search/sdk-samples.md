---
title: Примеры пакета SDK для Поиска в Интернете Bing
titleSuffix: Azure Cognitive Services
description: Используйте пакет SDK для Поиска в Интернете Bing, чтобы добавить возможности поиска в приложение Python, Node.js, C# или Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350438"
---
# <a name="bing-web-search-sdk-samples"></a>Примеры пакета SDK для Поиска в Интернете Bing

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Пакет SDK для Поиска в Интернете Bing доступен в Python, Node.js, C# и Java. Примеры кода, предварительные требования и инструкции по сборке предоставляются на GitHub. Описываются следующие сценарии:

* Запрос одного слова и вывод имени и URL-адреса первого результата для веб-страниц, изображений, новостных статей и видео.
* Запрос по фразе, проверка количества результатов и вывод имени и URL-адреса первого результата.
* Запрос по условию поиска с фильтром ответов `news` и вывод сведений о найденных новостях.
* Запрос по условию поиска с параметрами `answerCount` и `promote` с последующим выводом сведений о результатах.

## <a name="sdks-and-libraries"></a>SDK и библиотеки

Используйте эти ссылки, чтобы получить доступ к пакету SDK для предпочитаемого языка.

* Начало работы с [примерами для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples).
  * Ознакомьтесь также с определениями и зависимостями в [библиотеках Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch).
* Начало работы с [примерами для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples).
  * См. также статью [о пакете SDK для Node.js для поиска в Интернете](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Начало работы с [примерами для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
  * [Пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Ознакомьтесь также с определениями и зависимостями в [библиотеках .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch).
* Начало работы с [примерами для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples).
  * Ознакомьтесь также с определениями и зависимостями в [библиотеках Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch).