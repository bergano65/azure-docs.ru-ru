---
title: Учебник по разбиению результатов поиска на страницы в C#
titleSuffix: Azure Cognitive Search
description: Этот учебник является продолжением проекта по созданию первого приложения в службе "Когнитивный поиск Azure". Здесь вы можете ознакомиться с двумя способами разбиения на страницы. В первом используется набор кнопок с номерами страниц, и дополнительные кнопки для первой, предыдущей, следующей и последней страниц. Вторая система разбиения на страницы использует бесконечную прокрутку, которая включается при перемещении вертикальной полосы прокрутки до нижнего предела.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 935e6d43cf77d94b485d55eb4bc5eb517bf802a0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794003"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>Руководство по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure

Узнайте, как реализовать две разные системы разбиения на страницы: одну на основе номеров страниц и вторую с бесконечной прокруткой. Обе системы разбиения на страницы широко используются, и правильный выбор между ними зависит от того, какое взаимодействие с пользователем вы намерены организовать. Этот учебник создает системы разбиения на страницы в проекте, который был создан в [учебнике по созданию приложения для службы "Когнитивный поиск Azure"](tutorial-csharp-create-first-app.md).

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * добавление страниц в приложение;
> * добавление бесконечной прокрутки в приложение.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

Подготовьте и запустите проект, как описано в [учебнике C# по созданию первого приложения на C# для Когнитивного поиска Azure](tutorial-csharp-create-first-app.md). Это может быть ваша собственная версия проекта либо версия, установленная с сайта GitHub: [Создание первого приложения](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>добавление страниц в приложение;

Разбиение на страницы с номерами страниц стандартно применяется во всех крупнейших поисковых системах и на большинстве других веб-сайтов с возможностью поиска. Система с номерами страниц обычно дополняется элементами для перехода на "следующую" и "предыдущую" страницы, помимо списка с порядковыми номерами страниц. Иногда бывают доступны и элементы "первая страница" и "последняя страница". Эти варианты дают пользователю уверенный контроль над навигацией по страницам с результатами.

Здесь мы добавим систему с элементами для перехода к первой, предыдущей, следующей и последней страницам, а также с номерами страниц, которые начинаются не с 1, а с близких к текущей странице номеров (например, если пользователь просматривает страницу 10, будут отображаться номера для страниц 8, 9, 10, 11 и 12).

Система будет достаточно гибкой, чтобы настраивать количество номеров отображаемых страниц через глобальную переменную.

Система будет считать крайние номера страниц слева и справа особенными, то есть изменяющими диапазон отображаемых номеров страниц. Например, если отображаются номера страниц 8, 9, 10, 11 и 12, то после нажатия пользователем номера 8 система отображает другой диапазон номеров страниц: 6, 7, 8, 9 и 10. Аналогичный сдвиг вправо происходит при выборе номера 12.

### <a name="add-paging-fields-to-the-model"></a>Добавление полей разбиения на страницы в модель

Откройте базовое решение страницы поиска.

1. Откройте файл модели SearchData.cs.

2. Для начала добавьте несколько глобальных переменных. В MVC глобальные переменные объявляются в отдельном статическом классе. **ResultsPerPage** задает количество результатов на странице. **MaxPageRange** определяет количество отображаемых номеров страниц. **PageRangeDelta** определяет, на сколько страниц влево или вправо сдвигается диапазон страниц при выборе крайнего номера страницы слева или справа. Обычно это последнее значение составляет около половины от **MaxPageRange**. Добавьте следующий код в пространство имен.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Если проект выполняется на устройстве с небольшим экраном, например на ноутбуке, для параметра **ResultsPerPage** можно применить значение 2.

3. Добавьте свойства разбиения на страницы в класс **SearchData**, например сразу за свойством **searchText**.

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Добавление таблицы с элементами разбиения на страницы в представление

1. Откройте файл index.cshtml и добавьте следующий код сразу перед закрывающим тегом &lt;/body&gt;. Этот код представляет таблицу для элементов разбиения на страницы: первая, предыдущая, 1, 2, 3, 4, 5, следующая, последняя.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Чтобы все выглядело аккуратно, мы используем таблицу HTML. Но все наши действия определены в инструкциях @Html.ActionLink, каждая из которых вызывает контроллер с **новой** моделью, для которой указаны другие значения добавленного ранее свойства **paging**.

    Элементы для перехода к первой и последней страницам отправляют не строковые значения "первый" и "последний", а правильные номера страниц.

2. Добавьте несколько классов для разбиения на страницы в список стилей HTML в файле hotels.css. Класс **PageSelected** определяет страницу, которую пользователь просматривает в настоящее время (он выделяет номер полужирным шрифтом в списке номеров страниц).

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Добавление действия Page в контроллер

1. Откройте файл HomeController.cs и добавьте в него действие **Page**. Это действие реагирует на выбор любого элемента перехода к другой странице.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    Метод **RunQueryAsync** теперь будет возвращать синтаксическую ошибку, так как у нас появился третий параметр, обработку которого мы добавим чуть позже.

    > [!Note]
    > Класс **TempData** сохраняет значение (**object**) во временное хранилище, которое действует _только_ на протяжении одного вызова. То, что сохраняется во временном хранилище данных, будет доступно для следующего вызова действия контроллера, но исчезнет сразу после этого вызова. Такое короткое время существования означает, что текст для поиска и параметры разбиения на страницы нужно сохранять во временное хранилище при каждом вызове действия **Page**.

2. Действие **Index(model)** нужно обновить так, чтобы оно сохраняло временные переменные, а также передавало в вызов **RunQueryAsync** крайний левый параметр, предназначенный для разбиения на страницы.

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Метод **RunQueryAsync** придется изменить довольно сильно. Мы применим поля **Skip**, **Top** и **IncludeTotalResultCount** из класса **SearchParameters**, чтобы запросить выдачу только одной страницы результатов, начиная с номера **Skip**. Нам также нужно вычислить переменные для отображения элементов разбиения на страницы. Замените весь этот метод приведенным ниже кодом.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. И наконец, внесем небольшое изменение в код представления. Переменная **resultsList.Results.Count** теперь будет содержать число результатов, возвращаемых на одной странице (3 в нашем примере), а не общее число результатов. Так как **IncludeTotalResultCount** имеет значение true, переменная **resultsList.Count** теперь содержит общее число результатов. Найдите, где в представлении отображается количество результатов, и замените этот код следующим фрагментом.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Если вы присвоите переменной **IncludeTotalResultCount** значение true, производительность (обычно ненамного) снизится, так как Когнитивный поиск Azure будет каждый раз вычислять это значение. Для очень сложных наборов данных действует предупреждение о том, что полученный результат лишь _приблизительный_. Но для данных о гостиницах в нашем примере он будут точным.

### <a name="compile-and-run-the-app"></a>Компиляция и запуск приложения

Теперь нажмите кнопку **Запуск без отладчика** или клавишу F5.

1. Введите в поиск любой текст, который возвращает большое число результатов (например, wifi). Удается ли вам аккуратно пролистывать результаты?

    ![Нумерованный постраничный просмотр результатов по запросу "pool"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Попробуйте нажать крайний правый номер страницы, а затем крайний левый. Правильно ли изменяются номера страниц в соответствии с той страницей, которую вы просматриваете?

3. Полезны ли варианты страниц "первая" и "последняя"? Некоторые популярные системы веб-поиска их используют, а другие — нет.

4. Перейдите к последней странице результатов. Это единственная странице, которая может содержать меньше результатов, чем значение **ResultsPerPage**.

    ![Просмотр последней страницы результатов по запросу "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Введите строку "town" (город) и нажмите кнопку поиска. Если количество результатов умещается на одной странице, элементы разбиения на страницы не отображаются.

    ![Поиск по строке "town"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Теперь сохраните этот проект и закройте его. Мы попробуем реализовать альтернативный вариант разбиения на страницы.

## <a name="extend-your-app-with-infinite-scrolling"></a>Добавление бесконечной прокрутки в приложение

Бесконечная прокрутка активируется, когда пользователь доводит вертикальную полосу прокрутки до последнего из отображаемых результатов. В этом случае выполняется вызов к серверу для получения следующей страницы результатов. Если результатов больше нет, возвращается пустой результат и вертикальная полоса прокрутки не изменяется. Если имеются дополнительные результаты, они добавляются к текущей странице с соответствующим изменением положения полосы прокрутки.

Здесь важно то, что отображаемая страница не заменяется, а просто дополняется новыми результатами. Пользователь может всегда прокрутить страницу обратно к первым результатам поиска.

Чтобы реализовать бесконечную прокрутку, мы начнем с того состояния проекта, в котором еще не было элементов для перехода по номерам страниц. Поэтому для начала создайте еще одну копию базовой страницы поиска из репозитория GitHub: [Создание первого приложения](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Добавление полей разбиения на страницы в модель

1. Во-первых, добавьте свойство **paging** в класс **SearchData** в файле модели SearchData.cs.

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Эта строковая переменная содержит значение "next" (далее), если отправляется очередная страница результатов, или значение NULL, если отображается первая страница результатов поиска.

2. В этом же файле и в том же пространстве имен добавьте класс глобальных переменных с одним свойством. В MVC глобальные переменные объявляются в отдельном статическом классе. **ResultsPerPage** задает количество результатов на странице. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Добавление вертикальной полосы прокрутки в представление

1. Найдите раздел файла index.cshtml, который отображает результаты (он начинается со строки **@if (Model != null)** ).

2. Замените этот фрагмент следующим кодом: Новый раздел **&lt;div&gt;** содержит прокручиваемую область и добавляет новый атрибут **overflow-y** и вызов функции **onscroll** с именем scrolled(), как показано ниже.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Сразу после цикла и тега &lt;/div&gt; добавьте функцию **scrolled**.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    Инструкция **if** в представленном выше скрипте проверяет, дошел ли пользователь до нижнего положения вертикальной полосы прокрутки. Если это так, выполняется действие **Next** из контроллера **Home**. Этот контроллер не требует никакой информации и просто возвращает следующую страницу данных. Затем эти данные форматируются с применением тех же стилей HTML, что и для исходной страницы. Если результаты не возвращаются, данные на странице сохраняют прежний вид.

### <a name="handle-the-next-action"></a>Обработка действия Next

Контроллеру могут отправляться только три действия: первый запуск приложения вызывает **Index()** , первый поиск вызывает **Index(model)** , а все последующие обращения запрашивают дополнительные результаты через действие **Next(model)** .

1. Откройте файл контроллера Home и удалите метод **RunQueryAsync**, который был создан в прошлом учебнике.

2. Замените действие **Index(model)** следующим кодом: Теперь код обрабатывает поле **paging**, если оно содержит значение NULL или next, а затем выполняет вызов к Когнитивному поиску Azure.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Как и в методе разбиения на страницы с номерами, мы используем параметры поиска **Skip** и **Top** для получения только нужного подмножества данных.

3. Добавьте действие **Next** в контроллер Home. Обратите внимание, что он возвращает список отелей, каждый из элементов которого добавляет два элемента: название и описание отеля. Используется тот же формат, что и в функции **scrolled** для отображения возвращаемых данных в представлении.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Если вы получите синтаксическую ошибку в вызове **List&lt;string&gt;** , добавьте следующую директиву **using** в заголовок файла контроллера.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Компиляция и запуск проекта

Теперь нажмите кнопку **Запуск без отладчика** или клавишу F5.

1. Введите термин, который возвращает много результатов поиска, например "pool" (бассейн), и проверьте работу вертикальной полосы прокрутки. Подгружает ли она новую страницу результатов?

    ![Просмотр результатов по запросу "pool" с бесконечной прокруткой](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Чтобы на первой странице всегда отображалась полоса прокрутки, число результатов на ней должно превышать высоту области, в которой они отображаются. В нашем примере **.box1** имеет высоту 30 пикселей, а **.box2** — высоту 100 пикселей _и_ нижнее поле высотой 24 точки. Таким образом, каждая запись использует 154 пикселя. Три записи займут 3 x 154 = 462 пикселя в высоту. Чтобы всегда появлялась вертикальная полоса прокрутки, высота области отображения должна быть меньше 462 пикселей, подойдет даже значение 461. Такая проблема возникает только на первой странице, после этого полоса прокрутки отображается гарантированно. Следует изменить следующую строку: **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Прокрутите список результатов до конца. Обратите внимание, что вся информация теперь отображается на одной странице представления. Вы можете спокойно прокручивать ее до самого верха, не активируя новых вызовов к серверу.

Более сложные системы бесконечной прокрутки могут использовать для загрузки новой страницы результатов колесико мыши или другие механизмы. В рамках этого учебника мы не будем усложнять систему бесконечной прокрутки, но такие решения выглядят довольно привлекательно и позволяют снизить число кликов мышью, так что советуем вам дополнительно изучить их!

## <a name="takeaways"></a>Общие выводы

По результатам этого проекта можно сделать такие выводы:

* Нумерованная система разбиения на страницы хорошо подходит для поисков, в которых порядок результатов достаточно произволен, то есть на любой из страниц может появиться что-то интересное для пользователя.
* Бесконечная прокрутка удобна в тех случаях, когда порядок результатов очень важен. Например, если результаты сортируются по расстоянию от центра нужного города.
* Нумерованное разбиение на страницы более удобно для навигации. Например, пользователь может запомнить, что интересный для него результат расположен на странице 6. В варианте с бесконечной прокруткой такие простые ориентиры отсутствуют.
* Бесконечная прокрутка привлекает простотой, так как позволяет использовать привычное перемещение вверх и вниз без лишних щелчков по номерам страниц.
* Основная особенность бесконечный прокрутки заключается в том, что результаты добавляются на существующую страницу, а не заменяют ее, что повышает эффективность.
* Временное хранилище сохраняет данные только в течение одного вызова, а для последующих вызовов его необходимо обновлять.


## <a name="next-steps"></a>Дополнительная информация

Разбиение на страницы — очень важная функция для веб-поиска. Теперь, когда мы тщательно его изучили, можно перейти к упреждающим поисковым запросам, которые дополнительно улучшат взаимодействие с пользователем.

> [!div class="nextstepaction"]
> [Руководство для разработчиков C#. Добавление функции автозаполнения и предложений — Когнитивный поиск Azure](tutorial-csharp-type-ahead-and-suggestions.md)
