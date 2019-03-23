---
title: Автозаполнения пример для добавления условия в раскрывающемся списке в поле поиска — поиска Azure
description: Добавьте входные данные предложенных запросов путем создания средства подбора и составления запросов, которые вызывают предлагаемых запросов в службе поиска Azure.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373080"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Пример: Добавление автозаполнения для выбранных элементов для запроса раскрывающийся список

Поиск термина входы могут включать раскрывающийся список предложенных запросов. Вы уже видели эту возможность в коммерческих поисковых систем, и вы можете реализовать аналогичное поведение в поиска Azure с помощью [средство подбора конструкция](index-add-suggesters.md) и операции suggestions на запрос. В этой статье используются примеры для демонстрации формулировку запроса автозаполнения, с помощью средства подбора, который уже определен. 

## <a name="rest-api"></a>REST API

Можно использовать [Postman](search-fiddler.md) или [PowerShell](search-create-index-rest-api.md) и [REST API](https://docs.microsoft.com/rest/api/searchservice/) на пробное использование в этом примере, но результаты будут возвращены в виде документов JSON. Более реалистичный и visual можно найти, используя [пример кода для автозаполнения](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - индекс с помощью construct средства подбора

Автозаполнения начинается с [средство подбора конструкция](index-add-suggesters.md) добавлен в индекс, состоящий из полей, которые влияют значения раскрывающегося списка. Учитывая следующую схему, предлагаемых запросов будет создавать с помощью значений из поля «Категория» и «hotelName.

При условии, что минимальный образцы наборов данных, найти в кратких руководствах, отель имена включают «Затейливого оставаться» и «Roach motel» и категории включают «Luxury» и «Бюджет».

При наличии индекса отелей следует удалите и повторно используя следующую схему. Эта схема добавляет средство подбора. Не забудьте перезагрузить данные также.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - запрос с оператором предложения

Для использования средств подбора и вызова автозаполнения, необходимо отправить [API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) запроса с помощью GET или POST. На такой запрос служба поиска проверяет наличие возможных совпадений после получения первые три символа. 

В заголовке запроса задайте **ключ api** с ключом администратора или запрос, и **Content-Type** application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Запрос сканирования, все поля включены в средства подбора (hotelName и категории), возвращая следующий ответ:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Чтобы доставить ожидаемый результат, код клиента будет отрисовка в раскрывающемся списке в разделе панели поиска.

## <a name="net-sdk-c"></a>ПАКЕТ SDK ДЛЯ .NET (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - индекс с помощью construct средства подбора

В пакет SDK для .NET, используйте [средство подбора класс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Средство подбора — это коллекция, но он может принять только один элемент.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - запросов с помощью метода предложить

[DocumentsOperationsExtensions.Suggest метод](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) используется для возврата строк, предложенных запросов.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>См. также

+ [Изучить API REST, с помощью Postman](search-fiddler.md)
+ [Пример. Автозаполнение](search-autocomplete-tutorial.md)
+ [Добавление средств подбора в индекс](index-add-suggesters.md)
+ [Добавление средств подбора класса с помощью .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Вызов предложения, используя GET или POST (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Вызовите предложения, используя SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) или 