---
title: Краткое руководство по использованию клиентской библиотеки Визуального поиска Bing для C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: bfecebd0e475536ba88022b62c9ae31a31b2ff3c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88934428"
---
Ознакомьтесь с этим кратким руководством, чтобы начать получать аналитические сведения об изображении из службы "Визуальный поиск Bing" с помощью клиентской библиотеки для C#. Поскольку REST API Визуального поиска Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы с вашими приложениями. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/) | [Примеры](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
* Пакет NuGet для визуального поиска. 
    - В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект и выберите `Manage NuGet Packages` в меню. Установите пакет `Microsoft.Azure.CognitiveServices.Search.VisualSearch`. При установке пакетов NuGet также устанавливаются следующие пакеты.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. В Visual Studio создайте новый проект. Затем добавьте следующие директивы.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Создайте экземпляр клиента с помощью ключа подписки.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Отправка поискового запроса 

1. Создайте `FileStream` для ваших изображений (в данном случае `TestImages/image.jpg`). Затем используйте клиент для отправки поискового запроса с помощью`client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Проанализируйте результаты предыдущего запроса.

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по одностраничным веб-приложениям для наглядного поиска](../../tutorial-bing-visual-search-single-page-app.md)
