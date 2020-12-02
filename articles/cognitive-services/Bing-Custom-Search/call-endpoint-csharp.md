---
title: Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью C# | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Используйте это краткое руководство, чтобы начать запрос результатов поиска из Пользовательского поиска Bing в C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b892194a0e716aa3de218bc6edb6c38cdc898935
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338662"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Краткое руководство. Вызов конечной точки службы "Пользовательский поиск Bing" с помощью C# 

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Узнайте, как запрашивать результаты поиска из экземпляра Пользовательского поиска Bing. Хотя это приложение написано на C#, API Пользовательского поиска Bing представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Предварительные требования

- Экземпляр службы "Пользовательский поиск Bing". Дополнительные сведения см. в [кратком руководстве Создание первого экземпляра Пользовательского поиска Bing](quick-start.md).
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core).
- Любой выпуск [Visual Studio 2019 или более поздней версии](https://www.visualstudio.com/downloads/).
- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
- Пакет NuGet [Пользовательского поиска Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0). 

   Чтобы установить этот пакет в Visual Studio, сделайте следующее: 
     1. В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. 
     2. Найдите и выберите *Microsoft.Azure.CognitiveServices.Search.CustomSearch*, а затем установите этот пакет.

   При установке пакета NuGet Пользовательского поиска Bing Visual Studio также устанавливает следующие пакеты:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте в Visual Studio новое консольное приложение C#. Затем добавьте в проект следующие пакеты.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Создайте следующие классы для хранения результатов поиска, возвращаемых API Пользовательского поиска Bing.

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. В основном методе проекта создайте следующие переменные для своего ключа подписки API Пользовательского поиска Bing, идентификатора настраиваемой конфигурации экземпляра поиска и условия поиска.

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Создайте URL-адрес запроса, добавив условие поиска к параметру запроса `q=` и идентификатор пользовательской конфигурации экземпляра поиска к параметру `customconfig=`. Разделите параметры символом `&`. Для значения переменной `url` можно использовать глобальную конечную точку в следующем коде или конечную точку [личного поддомена](../../cognitive-services/cognitive-services-custom-subdomains.md), отображаемую на портале Azure для вашего ресурса.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Отправка и получение поискового запроса 

1. Создайте запрос клиента и добавьте ключ подписки заголовка `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Выполните веб-поиск и получите ответ в виде объекта JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Обработка и просмотр результатов

- Выполните итерацию по объекту ответа, чтобы отобразить информацию о каждом результате поиска, включая его имя, URL-адрес и дату последнего просмотра веб-страницы.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-страницы пользовательского поиска](./tutorials/custom-search-web-page.md)