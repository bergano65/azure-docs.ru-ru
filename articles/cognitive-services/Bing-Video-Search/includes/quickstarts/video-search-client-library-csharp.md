---
title: Краткое руководство по использованию клиентской библиотеки Поиска видео Bing (C#)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929281"
---
Используйте это краткое руководство, чтобы начать поиск новостей с помощью клиентской библиотеки Поиск видео Bing для C#. Поскольку REST API Поиска видео Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код этого примера можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) с дополнительными возможностями и заметками.

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/downloads/).
* Платформа Json.NET, доступная в виде [пакета NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Чтобы добавить клиентскую библиотеку API "Поиск видео Bing" в проект, перейдите к параметру **Управление пакетами NuGet** в **обозревателе решений** в Visual Studio. Добавьте пакет `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

При установке [пакета SDK NuGet для Поиска видео](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) также будут установлены следующие зависимости:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Одностраничное приложение для поиска видео](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>См. также раздел 

* [Что такое API Bing для поиска видео?](../../overview.md)
* [Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
