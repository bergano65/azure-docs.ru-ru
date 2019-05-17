---
title: Поиск похожих изображений из предыдущих операций поиска с помощью токена ImageInsightsToken в визуальном поиске Bing
titleSuffix: Azure Cognitive Services
description: Использование пакета SDK для визуального поиска Bing с целью получения URL-адресов изображений, указанных токеном ImageInsightsToken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: rosh
ms.openlocfilehash: 3f28e540aeb1604c5df885fd54d243211beb40b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796345"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Поиск похожих изображений из предыдущих операций поиска с помощью токена ImageInsightsToken

Пакет SDK для визуального поиска позволяет находить изображения в Интернете из предыдущих операций поиска, возвращающих `ImageInsightsToken`. Это приложение получает `ImageInsightsToken` и использует токен в последующих операциях поиска. После этого он отправляет `ImageInsightsToken` Bing и возвращает результаты, которые включают URL-адреса поиска Bing и URL-адреса, похожих изображений, найти в Интернете.

Полный исходный код для этого руководства можно найти дополнительную обработку ошибок и заметок на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Технические условия

* Любой выпуск [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Если вы используете Linux и Mac OS, можно запустить это приложение с помощью [Mono](https://www.mono-project.com/).
* Пакеты NuGet для визуального поиска и поиска изображений.
    - В обозревателе решений в Visual Studio, щелкните правой кнопкой мыши проект и выберите **управление пакетами NuGet** в меню. Установите пакеты `Microsoft.Azure.CognitiveServices.Search.CustomSearch` и `Microsoft.Azure.CognitiveServices.Search.ImageSearch`. При установке пакетов NuGet также устанавливаются следующие пакеты.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Получение токена ImageInsightsToken из пакета SDK для поиска изображений Bing

Это приложение использует `ImageInsightsToken`, полученный с помощью [пакета SDK для поиска изображений Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). В новом консольном приложении C# создайте клиент для вызова API с помощью `ImageSearchAPI()`. Затем с помощью `SearchAsync()` с запросом:

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Сохраните результат первого поиска с помощью `imageResults.Value.First()`, а затем сохраните токен `ImageInsightsToken` аналитических сведений изображения.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Это `ImageInsightsToken` отправляется визуального поиска Bing в запросе.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Указание токена ImageInsightsToken в запросе визуального поиска

Укажите `ImageInsightsToken` для запроса визуального поиска, создав объект `ImageInfo` из `ImageInsightsToken`, содержащихся в ответах от визуального поиска Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Использование визуального поиска для обнаружения изображений из токена ImageInsightsToken

Объект `VisualSearchRequest` содержит сведения об изображении, поиск которого выполняется в `ImageInfo`. Метод `VisualSearchMethodAsync()` получает результаты. Указывать двоичный файл изображения не нужно, так как изображение представлено токеном.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Итерация по результатам визуального поиска

Результатами визуального поиска являются объекты `ImageTag`. Каждый тег содержит список объектов `ImageAction`. Каждый `ImageAction` содержит `Data` поля, что список значений, которые зависят от типа действия. Например, можно выполнить итерацию по объектам `ImageTag` в списке `visualSearchResults.Tags` и получить содержащийся в нем тег `ImageAction`. Следующий пример выводит сведения о `PagesIncluding` действия:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>Типы действий PagesIncluding

Для получения URL-адресов фактических изображений из типов действий требуется приведение к типу, который считывает `ActionType` как `ImageModuleAction`, содержащий элемент `Data` со списком значений. Каждое значение является URL-адресом изображения.  Следующих приведений `PagesIncluding` тип действия, чтобы `ImageModuleAction` и считывает значения:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Дополнительные сведения об этих типах данных см. в статье [Images - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Изображения — визуальный поиск).

## <a name="returned-urls"></a>Возвращаемые URL-адреса

Готовое приложение возвращает указанные далее URL-адреса.

|Тип действия  |URL-адрес  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Как показано выше, типы `TopicResults` и `ImageResults` содержат запросы для связанных изображений. URL-адреса ведут на результаты поиска Bing.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Create a Visual Search single-page web app](tutorial-bing-visual-search-single-page-app.md) (Создание одностраничного веб-приложения Визуального поиска)
