---
title: Filter on search results
titleSuffix: Azure Cognitive Search
description: Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f4ce3cd0db20f76aa6169f15254cf36ee64151a5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406747"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

A *filter* provides criteria for selecting documents used in an Azure Cognitive Search query. Неотфильтрованные результаты поиска содержат все документы в индексе. Фильтр ограничивает поисковой запрос подмножеством документов. Например, фильтр может ограничивать полнотекстовый поиск продуктами определенного бренда, цвета или ценового диапазона, превышающего определенное пороговое значение.

Некоторые поисковые запросы устанавливают требования к фильтрам как часть реализации, но вы можете использовать фильтры, когда требуется ограничить поиск с помощью критериев *на основе значений* (поиск с ограничением поиска по типу продукта "книги" для категории "не фантастика", издательства Simon & Schuster).

Если вместо этого требуется выполнить поиск по определенным *структурам* данных (поиск с ограничением по полю "отзывы клиентов"), существуют альтернативные методы, описанные ниже.

## <a name="when-to-use-a-filter"></a>Когда следует использовать фильтр

Фильтры основаны на нескольких поисковых функциях, включая функцию поиска соседних объектов, навигацию по аспектам и фильтры безопасности, которые показывают только разрешенные документы. При реализации любой из этих функций требуется фильтр. Это фильтр, прикрепленный к поисковому запросу, который предоставляет координаты геолокации, выбранную пользователем категорию аспекта или идентификатор безопасности запрашивающего.

Ниже приведены примеры сценариев:

1. Используйте фильтр для создания среза индекса на основе значений данных в индексе. Учитывая схему с городом, типом жилья и удобствами, вы можете создать фильтр, чтобы явно выбирать документы, которые удовлетворяют вашим критериям (в Сиэтле, кондоминиумы, набережная). 

   Полнотекстовый поиск в одних и тех же входных данных часто приводит к аналогичным результатам, а его точность зависит от совпадения термина фильтра с содержимым вашего индекса. 

2. Используйте фильтр, если он требуется для поиска.

   * [Навигация по аспектам](search-faceted-navigation.md) использует фильтр для возврата категории аспекта, выбранной пользователем.
   * Геопространственный поиск использует фильтр для передачи координат текущего местоположения в приложениях с возможностью поиска соседних объектов. 
   * Фильтры безопасности передают идентификаторы безопасности в качестве критериев фильтра, где совпадение в индексе служит в качестве прокси-сервера для получения прав доступа к документу.

3. Используйте фильтр, если вы хотите использовать критерии поиска в числовом поле. 

   Числовые поля можно получить в документе, а также они могут отображаться в результатах поиска, но индивидуально они недоступны для поиска (при условии полнотекстового поиска). Если вам нужны критерии выбора, основанные на числовых данных, используйте фильтр.

### <a name="alternative-methods-for-reducing-scope"></a>Альтернативные методы для сокращения области поиска

Если требуется сузить область поиска в результатах, можно использовать не только фильтры. Эти возможности могут быть более эффективными в зависимости от задачи:

 + Параметр запроса `searchFields` ограничивает поиск конкретными полями. Например, если в вашем индексе содержатся отдельные поля для описания на английском и испанском языках, вы можете использовать searchFields, чтобы указать конкретные поля для полнотекстового поиска. 

+ Параметр `$select` используется, чтобы указать поля, которые требуется включить в результирующий набор для эффективной обрезки ответа перед его отправкой вызывающему приложению. This parameter does not refine the query or reduce the document collection, but if a smaller response is your goal, this parameter is an option to consider. 

Дополнительные сведения о параметрах запроса см. в подразделе ["Параметры запроса" раздела "Запрос" статьи Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) (Поиск документов (REST API службы "Поиск" Azure)).


## <a name="how-filters-are-executed"></a>How filters are executed

At query time, a filter parser accepts criteria as input, converts the expression into atomic Boolean expressions represented as a tree, and then evaluates the filter tree over filterable fields in an index.

Filtering occurs in tandem with search, qualifying which documents to include in downstream processing for document retrieval and relevance scoring. When paired with a search string, the filter effectively reduces the recall set of the subsequent search operation. При использовании отдельно (например, когда строка запроса пуста, где `search=*`), критерием фильтра являются только входные данные. 

## <a name="defining-filters"></a>Определение фильтров
Filters are OData expressions, articulated using a [subset of OData V4 syntax supported in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

You can specify one filter for each **search** operation, but the filter itself can include multiple fields, multiple criteria, and if you use an **ismatch** function, multiple full-text search expressions. In a multi-part filter expression, you can specify predicates in any order (subject to the rules of operator precedence). Попытка поставить предикаты в определенной последовательности не даст значительного прироста производительности.

One of the limits on a filter expression is the maximum size limit of the request. Весь запрос, включая функции фильтра, не может содержать более 16 МБ данных для POST или 8 КБ для GET. There is also a limit on the number of clauses in your filter expression. Хорошее проверенное правило: если у вас есть сотни предложений, вы рискуете превысить лимит. Мы рекомендуем разрабатывать приложение таким образом, чтобы оно не создавало фильтры неограниченного размера.

Следующие примеры представляют собой прототипные определения фильтров в нескольких API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filter usage patterns

The following examples illustrate several usage patterns for filter scenarios. Дополнительные примеры можно найти в разделе [Примеры OData](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Автономный параметр **$filter** без строки запроса полезен, когда выражение фильтра может полностью определить интересующие документы. Без строки запроса не выполняется лексический или лингвистический анализ, нет оценки и рейтинга. Notice the search string is just an asterisk, which means "match all documents".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Сочетание строки запроса и **$filter**, где фильтр создает подмножество, а строка запроса предоставляет термины для полнотекстового поиска по отфильтрованному подмножеству. The addition of terms (walking distance theaters) introduces search scores in the results, where documents that best match the terms are ranked higher. Using a filter with a query string is the most common usage pattern.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Составные запросы, разделенные с помощью логического оператора or, каждый со своими критериями фильтра (например, beagles в категории dog или siamese в категории cat). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Исчерпывающие сведения о конкретных вариантах использования см. в следующих статьях:

+ [Фильтры аспекта](search-filters-facets.md)
+ [Фильтры языка](search-filters-language.md)
+ [Фильтрация в соответствии с политикой безопасности](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Требования к полям для фильтрации

In the REST API, filterable is *on* by default for simple fields. Фильтруемые поля увеличивают размер индекса. Не забудьте установить `"filterable": false` для полей, которые вы не планируете фактически использовать в фильтре. Дополнительные сведения о параметрах для определения полей см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса (REST API службы "Поиск Azure")).

В пакете SDK для .NET фильтруемые поля *отключены* по умолчанию. You can make a field filterable by setting the [IsFilterable property](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) of the corresponding [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object to `true`. You can also do this declaratively by using the [IsFilterable attribute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Making an existing field filterable

You can't modify existing fields to make them filterable. Instead, you need to add a new field, or rebuild the index. For more information about rebuilding an index or repopulating fields, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Принципы работы текстовых фильтров

Text filters match string fields against literal strings that you provide in the filter. Unlike full-text search, there is no lexical analysis or word-breaking for text filters, so comparisons are for exact matches only. For example, assume a field *f* contains "sunny day", `$filter=f eq 'Sunny'` does not match, but `$filter=f eq 'sunny day'` will. 

Текстовые строки учитывают регистр. There is no lower-casing of upper-cased words: `$filter=f eq 'Sunny day'` will not find "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Approaches for filtering on text

| Подход | Описание | Сценарии использования |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | A function that matches a field against a delimited list of strings. | Recommended for [security filters](search-security-trimming-for-azure-search.md) and for any filters where many raw text values need to be matched with a string field. The **search.in** function is designed for speed and is much faster than explicitly comparing the field against each string using `eq` and `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Функция, которая позволяет совместно использовать операции полнотекстового поиска вместе с операциями строго логического фильтра в одном выражении фильтра. | Use **search.ismatch** (or its scoring equivalent, **search.ismatchscoring**) when you want multiple search-filter combinations in one request. Вы также можете использовать ее для фильтра *contains* (для фильтрации в частичной строке в контексте большей строки). |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Определенное пользователем выражение, состоящее из поля, операторов и значений. | Use this when you want to find exact matches between a string field and a string value. |

## <a name="numeric-filter-fundamentals"></a>Основные компоненты числового фильтра

Числовые поля недоступны для поиска (`searchable`) в контексте полнотекстового поиска. Только строки подлежат полному текстовому поиску. Например, при вводе 99,99 в качестве поискового запроса вы не сможете найти товары по цене 99,99 доллара США. Вместо этого отображаются элементы, которые содержат строку поля документа с номером 99. Таким образом, если у вас есть числовые данные, предполагается, что вы будете использовать их для фильтров, включая диапазоны, аспекты, группы и т. д. 

Документы, содержащие числовые поля (цена, размер, SKU, идентификатор), предоставляют эти значения в результатах поиска, если поле отмечено `retrievable`. Суть в том, что полнотекстовый поиск не применим к числовым типам полей.

## <a name="next-steps"></a>Дальнейшие действия

Сначала попробуйте использовать **обозреватель поиска** на портале, чтобы отправить запросы с параметрами **$filter**. [Пример индекса выборки недвижимости](search-get-started-portal.md) предоставляет полезные результаты для следующих отфильтрованных запросов при их вставке в строку поиска:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Дополнительные примеры можно найти в разделе [Примеры OData](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Дополнительные материалы

+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Поиск по документам (REST API службы поиска Azure))
+ [Синтаксис простых запросов](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Supported data types (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) (Поддерживаемые типы данных (служба "Поиск Azure")).
