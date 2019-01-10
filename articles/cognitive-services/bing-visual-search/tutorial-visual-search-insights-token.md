---
title: Поиск похожих изображений из предыдущих операций поиска с помощью токена ImageInsightsToken в визуальном поиске Bing
titlesuffix: Azure Cognitive Services
description: Использование пакета SDK для визуального поиска Bing с целью получения URL-адресов изображений, указанных токеном ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 609e164209bb2c920a36f293cee146cfece15fb5
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742615"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Поиск похожих изображений из предыдущих операций поиска с помощью токена ImageInsightsToken

Пакет SDK для визуального поиска позволяет находить изображения в Интернете из предыдущих операций поиска, возвращающих `ImageInsightsToken`.  Это приложение получает `ImageInsightsToken` и использует токен в последующих операциях поиска. Затем оно отправляет `ImageInsightsToken` в Bing и возвращает результаты, содержащие URL-адреса поиска Bing и URL-адреса схожих изображений, найденных в Интернете.

Полный исходный код этого примера с дополнительными возможностями по обработке ошибок и аннотациями можно получить на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](http://www.mono-project.com/).
* Пакеты NuGet для визуального поиска и поиска изображений. 
    - В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект и выберите `Manage NuGet Packages` в меню. Установите пакеты `Microsoft.Azure.CognitiveServices.Search.CustomSearch` и `Microsoft.Azure.CognitiveServices.Search.ImageSearch`. При установке пакетов NuGet также устанавливаются следующие пакеты.
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json.


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Получение токена ImageInsightsToken из пакета SDK для поиска изображений Bing

Это приложение использует `ImageInsightsToken`, полученный с помощью [пакета SDK для поиска изображений Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). В новом консольном приложении C# создайте клиент для вызова API с помощью `ImageSearchAPI()`. Затем используйте `SearchAsync()` в запросе.

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
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

Этот `ImageInsightsToken` будет отправлен в визуальный поиск Bing в запросе.

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

Результатами визуального поиска являются объекты `ImageTag`.  Каждый тег содержит список объектов `ImageAction`.  Каждый объект `ImageAction` содержит поле `Data`, которое представляет собой список значений, зависящих от типа действия. Например, можно выполнить итерацию по объектам `ImageTag` в списке `visualSearchResults.Tags` и получить содержащийся в нем тег `ImageAction`. В следующем примере показан вывод сведений о действиях `PagesIncluding`.

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

Для получения URL-адресов фактических изображений из типов действий требуется приведение к типу, который считывает `ActionType` как `ImageModuleAction`, содержащий элемент `Data` со списком значений.  Каждое значение является URL-адресом изображения.  Далее показано приведение типа действия `PagesIncluding` к `ImageModuleAction` и считывание значений.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorial-bing-visual-search-single-page-app.md)