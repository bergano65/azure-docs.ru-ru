---
title: Краткое руководство по использованию клиентских библиотек Пользовательского поиска Bing (C#)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: b722fd34a78f1e9c2f4a660c205cf4a1e163a5d7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253060"
---
Приступите к работе с клиентской библиотекой службы Пользовательского поиска Bing для C#. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба "API пользовательского поиска Bing" позволяет создавать специально адаптированные интерфейсы поиска без рекламы по темам, которые действительно важны для вас. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Клиентская библиотека Пользовательского поиска Bing для C# позволяет выполнить следующие действия.
* Найти результаты поиска в Интернете из вашего экземпляра API Пользовательского поиска Bing.

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [Примеры](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". См. [Краткое руководство. Создание первого экземпляра службы "Пользовательский поиск Bing"](../../quick-start.md), чтобы получить дополнительные сведения.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Любой выпуск [Visual Studio 2017 или более поздней версии](https://www.visualstudio.com/downloads/)
- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
- Пакет NuGet [Пользовательского поиска Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet) в меню. Установите пакет `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. При установке пакета NuGet для пользовательского поиска также будут установлены следующие сборки:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
> [Build a Custom Search web app](../../tutorials/custom-search-web-page.md)
