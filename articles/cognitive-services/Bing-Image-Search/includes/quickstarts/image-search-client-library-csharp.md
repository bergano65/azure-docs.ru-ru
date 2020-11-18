---
title: Краткое руководство по использованию клиентской библиотеки Поиска изображений Bing (C#)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 247aea49d60c2d953d8b6dff37d22188a5442a1f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625429"
---
Воспользуйтесь сведениями из краткого руководства и выполните первый поиск изображения с помощью клиентской библиотеки Поиска изображений Bing. 

Эта клиентская библиотека является программой-оболочкой для REST API и обладает такими же возможностями. 

Вы создадите приложение C#, которое отправляет запрос на поиск изображения, анализирует ответ JSON и отображает URL-адрес первого возвращенного изображения.

Исходный код, используемый в данном примере, вместе с дополнительной обработкой ошибок и аннотациями можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch).

## <a name="prerequisites"></a>Предварительные требования

* Если используется Windows, любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/vs/whatsnew/).
* Если используется macOS или Linux, [VS Code](https://code.visualstudio.com) с установленным [.NET Core](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install).
* [Бесплатная подписка Azure.](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

См. также [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Создайте консольный проект.

Сначала создайте консольное приложение C#.

## <a name="create-a-console-project"></a>Создайте консольный проект.

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Создайте консольное решение с именем *BingImageSearch* в Visual Studio.
    
1. Добавьте [пакет NuGet для Когнитивного поиска изображений](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch).
    1. В **Обозревателе решений** щелкните проект правой кнопкой мыши.
    1. Выберите **Manage NuGet Packages...** (Управление пакетами NuGet...).
    1. Найдите и выберите *Microsoft.Azure.CognitiveServices.Search.ImageSearch*, а затем установите этот пакет.
    
# <a name="vs-code"></a>[Код VS](#tab/vscode)

1. Откройте окно терминала в VS Code.
1. Создайте консольный проект с именем *BingImageSearch*, введя в окне терминала следующий код:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Откройте папку *BingImageSearch* в VS Code.
1. Добавьте NuGetPackage [пакет NuGet для Когнитивного поиска изображений](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch), введя следующий код в окне терминала:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Инициализация приложения


1. Замените все инструкции `using` в *Program.cs* следующим кодом:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. В методе `Main` проекта создайте переменные для допустимого ключа подписки, результатов поиска изображения, которые должны быть возвращены Bing, и условия поиска. Затем с помощью ключа создайте экземпляр клиента для поиска образа.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Отправка поискового запроса с помощью клиента
    
Там же в методе `Main` с помощью клиента выполните поиск с текстом запроса:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Синтаксический анализ и просмотр первого результата изображений

Выполните синтаксический анализ результатов изображений, возвращенных в ответе. 

Если ответ содержит результаты поиска, сохраните первый результат и распечатайте некоторые сведения о нем.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>См. также раздел

* [Что такое API Bing для поиска изображений?](../../overview.md)  
* [Попробуйте API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Примеры .NET для пакета SDK в Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Документация по службам Azure Cognitive Services](../../../index.yml)
* [Справочник по API Bing для поиска изображений](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)