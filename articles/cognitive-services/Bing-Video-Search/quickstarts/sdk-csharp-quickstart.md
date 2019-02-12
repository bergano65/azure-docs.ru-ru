---
title: Краткое руководство. Поиск видео с помощью пакета SDK Поиска видео Bing для C#
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве описано, как отправлять запросы с помощью пакета SDK Поиска видео Bing для C#.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: 820c6a57a4a8266b68c5a245c2c2cf316aa071a9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569146"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Краткое руководство. Поиск видео с помощью пакета SDK Поиска видео Bing для C#

Используйте это краткое руководство, чтобы начать поиск новостей с помощью пакета SDK Поиска видео Bing для C#. Пока для Поиска видео Bing имеется REST API, совместимый с большинством языков, пакет SDK предоставляет простой способ интегрировать службу в приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch). Он содержит больше аннотаций и функций.

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* Платформа Json.NET, доступная в виде [пакета NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Чтобы добавить пакет SDK Поиска видео Bing в проект, перейдите к параметру `Manage NuGet Packages` в обозревателе решений в Visual Studio.  Добавьте пакет `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

При установке [пакета SDK NuGet для Поиска видео](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) также будут установлены следующие зависимости:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Создание и инициализация проекта

1. Создайте консольное решение C# в Visual Studio. Затем добавьте следующие элементы в основной файл кода.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Создайте экземпляр клиента путем создания нового объекта `ApiKeyServiceClientCredentials` с помощью ключа подписки и вызова конструктора.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Отправка строки запроса и обработка результатов

1. Используйте клиент для отправки поискового запроса. В качестве поискового запроса используйте "SwiftKey".

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Если были возвращены какие-либо результаты, получите первый из них с помощью `videoResults.Value[0]`. Затем выполните печать идентификатора, заголовка и URL-адреса видео.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для поиска видео](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также 

* [Что такое API Bing для поиска видео?](../overview.md)
* [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
