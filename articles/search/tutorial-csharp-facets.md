---
title: Учебник по C#. Использование аспектов для улучшения навигации
titleSuffix: Azure Cognitive Search
description: Этот учебник основан на проекте "Разбиение результатов поиска на страницы (Когнитивный поиск Azure)" для добавления аспектной (фасетной) навигации. Узнайте, как с помощью аспектов можно легко сузить поиск.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9f0d716e9077b2d9702f26b1afe92d9e4faf4a77
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794078"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>Руководство по C#. Использование аспектов для улучшения навигации в службе "Когнитивный поиск Azure"

Аспекты используются для облегчения навигации, предоставляя пользователю набор ссылок для использования при поиске. Аспекты — это атрибуты данных (например, категория или особенность отеля в наших примерах данных).

Это руководство основано на проекте разбиения на страницы, который был создан в [руководстве по C# для Search results pagination — Azure Cognitive Search](tutorial-csharp-paging.md) (Учебник по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure).

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Пометка свойств модели как _IsFacetable_.
> * Добавление аспектной навигации в приложение.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

Подготовьте и запустите проект, как описано в [учебнике C# по Search results pagination — Azure Cognitive Search](tutorial-csharp-paging.md) (Учебник по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure). Это может быть ваша собственная версия проекта либо версия, установленная с сайта GitHub: [Создание первого приложения](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Пометка свойств модели как IsFacetable

Чтобы свойство модели находилось в аспектном поиске, оно должно быть помечено как **IsFacetable**.

1. Изучите класс **Hotel**. Например, свойства **Category** и **Tags** помечены как **IsFacetable**, а **HotelName** и **Description** — нет. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Мы не будем изменять какие-либо теги в этом учебнике, поэтому закройте файл hotel.cs без изменений.

    > [!Note]
    > Аспектный поиск выдает ошибку, если поле, запрошенное при поиске, не помечено соответствующим образом.


## <a name="add-facet-navigation-to-your-app"></a>Добавление аспектной навигации в приложение

В этом примере мы дадим пользователю возможность выбрать одну категорию отелей или одно удобство из списков ссылок, показанных слева от результатов. Пользователь начинает с ввода определенного поискового текста, а затем может сузить результаты поиска, выбрав категорию, и дополнительно сузить их, выбрав удобство (или сначала можно выбрать удобство).

Нам нужен контроллер для передачи списков аспектов в представление. Необходимо сохранять выбор пользователя в процессе поиска. В качестве механизма сохранения данных мы, опять-таки, используем временное хранилище.

![Использование аспектной навигации для сужения поиска по удобству "бассейн"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Добавление строк фильтра в модель SearchData

1. Откройте файл SearchData.cs и добавьте строковые свойства в класс **SearchData** для хранения строк фильтра аспектов.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Добавление метода действия Facet

Домашнему контроллеру требуется одно новое действие, **Facet**, и обновления к его существующим действиям **Index** и **Page**, а также обновления метода **RunQueryAsync**.

1. Откройте файл домашнего контроллера и добавьте оператор **using**, чтобы включить конструкцию **List&lt;string&gt;** .

    ```cs
    using System.Collections.Generic;
    ```

2. Замените метод действия **Index(SearchData model)** .

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Замените метод действия **Page(SearchData model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Добавьте метод действия **Facet (SearchData model)** , который будет активирован, когда пользователь щелкнет ссылку на аспект. Модель будет содержать либо фильтр поиска по категориям, либо фильтр поиска по удобствам. Добавьте это действие после действия **Page**.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Настройка фильтра поиска

Если пользователь выбирает определенный аспект, например он щелкает категорию **Resort and Spa**, тогда в результатах должны быть возвращены только отели, указанные в этой категории. Чтобы сузить поиск таким образом, нам нужно настроить _фильтр_.

1. Замените метод **RunQueryAsync** следующим кодом. Прежде всего, он принимает строку фильтра категории и строку фильтра удобства и устанавливает параметр **Filter** класса **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Мы добавили свойства **Category** и **Tags** в список элементов **Select** для возврата. Это дополнение не является обязательным условием для работы аспектной навигации, но мы используем эту информацию для проверки правильности фильтрации.

### <a name="add-lists-of-facet-links-to-the-view"></a>Добавление списков ссылок на аспекты в представление

В представление потребуется внести существенные изменения. 

1. Откройте файл hotels.css (в папке wwwroot/css) и добавьте следующие классы.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. Для представления мы упорядочим выходные данные в виде таблицы, чтобы разместить списки аспектов слева, а результаты — справа. Откройте файл index.cshtml. Замените все содержимое тегов &lt;body&gt; в HTML следующим кодом.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">|&lt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&lt;</p>
                                    }
                                </td>

                                @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                {
                                    <td class="tdPage">
                                        @if (Model.currentPage == pn)
                                        {
                                            // Convert displayed page numbers to 1-based and not 0-based.
                                            <p class="pageSelected">@(pn + 1)</p>
                                        }
                                        else
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;|</p>
                                    }
                                </td>
                            </tr>
                        </table>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Обратите внимание на использование вызова **Html.ActionLink**. Этот вызов передает действительные строки фильтра на контроллер, когда пользователь щелкает ссылку на аспект. 

### <a name="run-and-test-the-app"></a>Запуск и тестирование приложения

Преимущество аспектной навигации для пользователя заключается в том, что он может сузить поиск одним щелчком мыши. Это будет видно в следующей последовательности.

1. Запустите приложение и введите airport в качестве текста для поиска. Убедитесь, что список аспектов отображается слева. Эти аспекты — это все, что относится к отелям, в текстовых данных которых упоминается "аэропорт", с подсчетом частоты их появления.

    ![Использование аспектной навигации для сужения поиска по аэропортам](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Выберите категорию **Resort and Spa**. Убедитесь, что все результаты относятся к этой категории.

    ![Сужение поиска до Resort and Spa](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Щелкните удобство **континентальный завтрак**. Убедитесь, что все результаты по-прежнему относятся к категории Resort and Spa и содержат выбранные удобства.

    ![Сужение поиска до аспекта "континентальный завтрак"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Попробуйте выбрать любую другую категорию, а затем одно удобство и посмотрите суженные результаты поиска. Затем попробуйте другой способ (одно удобство, а затем одну категорию).

    >[!Note]
    > После выбора в списке аспектов (например, категории) любой предыдущий выбор в списке категорий будет переопределен.

## <a name="takeaways"></a>Общие выводы

По результатам этого проекта можно сделать такие выводы:

* Если свойства нужно включить в аспектную навигацию, их необходимо обязательно пометить как **IsFacetable**.
* Аспектная навигация предоставляет пользователю простой и интуитивно понятный способ сужения результатов поиска.
* Аспектную навигацию лучше всего разделить на разделы с соответствующими заголовками (категории отелей, удобства, диапазоны цен, диапазоны рейтингов и т. д.).

## <a name="next-steps"></a>Дополнительная информация

В следующем учебнике мы рассмотрим результаты бронирования. До этого момента результаты упорядочиваются просто — в том порядке, в котором они находятся в базе данных.

> [!div class="nextstepaction"]
> [Учебник C# по упорядочиванию результатов в службе "Когнитивный поиск Azure"](tutorial-csharp-orders.md)
