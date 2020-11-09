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
ms.openlocfilehash: b31335df7c1dc48f82699aa3676561721474071d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076220"
---
# <a name="bing-web-search-sdk-samples"></a>Примеры пакета SDK для Поиска в Интернете Bing

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](https://aka.ms/cogsvcs/bingmove).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](https://aka.ms/cogsvcs/bingmigration).

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
