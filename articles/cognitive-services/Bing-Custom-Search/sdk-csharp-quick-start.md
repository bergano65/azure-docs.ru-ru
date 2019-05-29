---
title: Краткое руководство. Вызов конечной точки Пользовательского поиска Bing с помощью пакета SDK для C# | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Установка компонентов консольного приложения пакета SDK для пользовательского поиска для C#.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 9e13edce77819d5ef8cfc3b6becff9fb82224a83
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595962"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>Краткое руководство. Вызов конечной точки Пользовательского поиска Bing с помощью пакета SDK для C# 

Используйте это краткое руководство,чтобы начать запрос результатов поиска из экземпляра Пользовательского поиска Bing с помощью пакета SDK для C#. Хотя Пользовательский поиск Bing имеет API REST, совместимый с большинством языков, пакет SDK для Пользовательского поиска Bing предоставляет простой способ интегрировать службу в приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](quick-start.md), чтобы получить дополнительные сведения.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Любой выпуск [Visual Studio 2017 или более поздней версии](https://www.visualstudio.com/downloads/)
- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
- Установленный пакет [NuGet для пользовательского поиска](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet) в меню. Установите пакет `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. При установке пакета NuGet для пользовательского поиска также будут установлены следующие сборки:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json.

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте в Visual Studio новое консольное приложение C#. Добавьте в проект следующие пакеты.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. В основном методе приложения создайте экземпляр клиента поиска с помощью ключа API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Отправка поискового запроса и получение ответа
    
1. Отправьте поисковой запрос с помощью метода `SearchAsync()` клиента и сохраните ответ. Замените `YOUR-CUSTOM-CONFIG-ID` идентификатором конфигурации вашего экземпляра (идентификатор можно найти на [портале Пользовательского поиска Bing](https://www.customsearch.ai/)). В этом примере выполняется поиск "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Метод `SearchAsync()` возвращает объект `WebData`. Используйте объект для выполнения итерации по любым найденным `WebPages`. Этот код находит первую соответствующую веб-страницу и выводит ее `Name` и `URL`.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
