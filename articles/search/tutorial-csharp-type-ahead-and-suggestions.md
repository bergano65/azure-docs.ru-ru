---
title: Руководство по C#. Функция автозаполнения и предложений
titleSuffix: Azure Cognitive Search
description: Добавьте функцию автозаполнения и показа предложений в виде раскрывающегося списка для сбора поисковых запросов пользователей. В основе учебника лежит имеющийся проект гостиниц.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 07/15/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 47c846f0d8d1cccd91301c7424b2618fffddd565
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998447"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Руководство по Добавление автозаполнения и предложений с помощью пакета SDK для .NET

Узнайте, как реализовать функцию автозаполнения (упреждающий ввод поисковых запросов и показ предложений), срабатывающую, когда пользователь начинает вводить информацию в поле поиска. В этом руководстве мы покажем автоматически завершенные запросы и результаты предложений по отдельности, а затем вместе. Пользователю может быть достаточно ввести два или три символа, чтобы увидеть все доступные результаты.

В этом руководстве описано следующее:
> [!div class="checklist"]
> * Добавление предложений
> * Добавление выделения для предложений
> * Добавление автозаполнения
> * Объединение функций автозаполнения и предложений

## <a name="prerequisites"></a>Предварительные требования

Это руководство является частью серии руководств и основано на проекте разбиения на страницы (процесс создания см.в [Руководстве по C#. Разбиение результатов поиска на страницы — Когнитивный поиск Azure](tutorial-csharp-paging.md)).

Кроме того, вы можете скачать уже готовое решение из этого репозитория GitHub: [3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Добавление предложений

Начнем с простейшего случая предоставления пользователю предложений из выпадающего списка.

1. В файле index.cshtml измените значение `@id` инструкции **TextBoxFor** на **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. В конце этой инструкции после закрывающего **&lt;/div&gt;** введите приведенный ниже скрипт. В этом скрипте используется [мини-приложение Autocomplete](https://api.jqueryui.com/autocomplete/) из библиотеки с открытым кодом jQuery UI для представления раскрывающегося списка предлагаемых результатов. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Идентификатор azureautosuggest используется для подключения указанного выше скрипта к полю поиска. Параметру source мини-приложения присваивается Suggest. Этот метод вызывает API Suggest с двумя параметрами запроса — **highlights** и **fuzzy**. Этим параметрам в данном экземпляре присвоено значение false. Кроме того, для активации функции поиска нужно не менее двух символов.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Добавление ссылок на скрипты jQuery в представление

1. Чтобы получить доступ к библиотеке jQuery, замените код в разделе &lt;head&gt; файла представления на приведенный ниже код.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Так как мы вводим новую ссылку jQuery, также нужно удалить или закомментировать ссылку jQuery по умолчанию в файле _Layout.cshtml (в папке **Views/Shared**). Найдите указанные ниже строки и закомментируйте первую строку скрипта, как показано ниже. Это изменение позволяет избежать конфликта ссылок на jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Теперь можно использовать предопределенные функции jQuery мини-приложения Autocomplete.

### <a name="add-the-suggest-action-to-the-controller"></a>Добавление действия Suggest в контроллер

1. Добавьте действие **Suggest** в контроллер Home, например, после действия **Page**.

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Параметр **Top** указывает количество результатов для возврата (если параметр не задан, значение по умолчанию равно 5). Объект _средство подбора_ указывается в индексе Azure, который выполняется при настройке данных, а не клиентским приложением, как в этом руководстве. В этом случае средство подбора называется sg, и оно выполняет поиск только в поле **HotelName**. 

    Нечеткое соответствие позволяет также включать в выходные данные "промахи" величиной до одного редакционного расстояния. Если для параметра **highlights** задано значение true, то в выходные данные добавляются полужирные метки HTML. В рамках следующего раздела мы установим значение true для этих двух параметров.

2. Могут возникнуть некоторые синтаксические ошибки. Если они возникли, добавьте в начало файла две приведенные ниже инструкции **using**.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Запустите приложение. Вы видите список предложенных вариантов при вводе, например, "po"? Теперь попробуйте ввести "pa".

    ![При вводе "po" отображаются два предложения](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Обратите внимание на то, что введенные буквы _должны_ находиться в начале слова, а не в какой-либо другой его части.

4. В скрипте представления задайте для параметра **&fuzzy** значение true и снова запустите приложение. Теперь введите "po". Обратите внимание: при поиске предполагается, что одна буква неправильная!
 
    ![Ввод "pa", когда для fuzzy задано значение true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Подробные сведения о логике, используемой в поиске по нечетким соответствиям, приведены в статье о [синтаксисе запросов Lucene в службе "Когнитивный поиск Azure"](./query-lucene-syntax.md).

## <a name="add-highlighting-to-the-suggestions"></a>Добавление выделения для предложений

Мы можем улучшить вид предложений для пользователя, задав для параметра **highlights** значение true. Но сначала необходимо добавить код в представление, чтобы отобразить текст полужирным шрифтом.

1. В представлении (index.cshtml) добавьте приведенный ниже скрипт после скрипта **azureautosuggest**, введенного выше.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. Теперь измените идентификатор текстового поля таким образом, чтобы оно выглядело, как показано ниже.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Снова запустите приложение. В предложениях отобразится введенный текст, выделенный полужирным. Введите, например, "ра".
 
    ![Ввод "pa" с выделением](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Логика, используемая в приведенном выше скрипте выделения, не защищена от случайных ошибок. Если ввести термин, который отображается дважды в одном и том же имени, выделенные полужирным шрифтом результаты будут не совсем такими, как хотелось бы. Попробуйте ввести "mo".

    Одним из вопросов, на которые должен ответить разработчик, является следующий: когда скрипт работает "достаточно хорошо", а когда нужно устранять его ошибки. В этом руководстве мы не будем больше касаться темы выделения. Но если оно неэффективно для ваших данных, рекомендуем найти более точный алгоритм. Дополнительные сведения см. в разделе [Выделение совпадений](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Добавление автозаполнения

Другим вариантом, который немного отличается от предложений, является автозавершение вводимого запроса (иногда его еще называют "упреждающий ввод"). Здесь мы также начнем с простой реализации, прежде чем улучшать взаимодействие с пользователем.

1. Введите приведенный ниже скрипт в представление после предыдущих скриптов.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. Теперь измените идентификатор текстового поля таким образом, чтобы оно выглядело, как показано ниже.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. В контроллере Home необходимо внести действие **Autocomplete**, например, после действия **Suggest**.

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Обратите внимание на то, что при автозаполнении используется та же функция *средства подбора* с именем "sg", что и для предложений (здесь мы пытаемся автоматически заполнить поле именем отеля).

    Есть ряд настроек **AutocompleteMode**. Здесь мы используем **OneTermWithContext**. Описание дополнительных параметров см. в разделе об [API автозавершения](/rest/api/searchservice/autocomplete).

4. Запустите приложение. Обратите внимание на то, что варианты, которые отображаются в раскрывающемся списке, являются отдельными словами. Введите слова, начинающиеся с "re". Обратите внимание на то, что количество предлагаемых вариантов уменьшается по мере набора букв.

    ![Ввод при базовых настройках автозаполнения](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    В сущности, скрипт предложений, который вы выполняли ранее, вероятно, более полезен, чем этот скрипт автозаполнения. Чтобы пользователю было удобнее работать с автозаполнением, лучше всего добавить его в поиск предложений.

## <a name="combine-autocompletion-and-suggestions"></a>Объединение функций автозаполнения и предложений

Объединение функций автозаполнения и предложений является самой сложной задачей и, возможно, обеспечивает максимальное удобство для пользователя. Нам необходимо, чтобы по мере набора текста отображался лучший вариант автозавершения из Когнитивного поиска Azure. Кроме того, мы хотим увидеть несколько предложений в виде раскрывающегося списка.

Есть библиотеки с этим функционалом, которые чаще всего называются "встроенное автозаполнение" или имеют подобное название. Но мы собираемся реализовать эту функцию как собственную, чтобы можно было видеть, что происходит. Сначала мы поработаем над контроллером в этом примере.

1. Нам нужно добавить контроллеру действие, которое возвращает только один результат автозаполнения вместе с указанным количеством предложений. Назовем это действие **AutocompleteAndSuggest**. В контроллере Home добавьте указанное ниже действие после других новых действий.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    В верхней части списка **results** отображается один вариант автозаполнения, а затем приведены остальные предложения.

2. В представлении мы сначала реализуем прием, чтобы светло-серое слово автозаполнения отображалось прямо под полужирным текстом, который вводит пользователь. Для этой цели в HTML предусмотрено относительное размещение. Измените инструкцию **TextBoxFor** (и окружающие ее инструкции &lt;div&gt;) на приведенные ниже. Обратите внимание на то, что второе поле поиска, обозначенное как **underneath**, находится прямо под полем обычного поиска, которое сдвинуто на 39 пикселей от своей позиции по умолчанию.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Обратите внимание на то, что мы снова изменяем идентификатор: в этом примере на **azureautocomplete**.

3. Кроме того, введите в представлении приведенный ниже скрипт после всех скриптов, которые ввели ранее. Скрипт довольно длинный.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Обратите внимание на продуманное использование функции **interval**. Она позволяет удалять основной текст, когда он больше не соответствует введенному, а также задавать тот же регистр (верхний или нижний), что и в тексте, который вводит пользователь (так как во время поиска "pa" соответствует "PA", "pA", "Pa"). После этого наложенный текст отображается должным образом.

    Ознакомьтесь с комментариями в скрипте, чтобы получить полное представление о нем.

4. В конце нам нужно немного скорректировать два класса HTML, чтобы сделать их прозрачными. Добавьте приведенную ниже строку к классам **searchBoxForm** и **searchBox** в файле hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Теперь запустите приложение. Введите "pa" в поле поиска. Происходит ли автозаполнение словом "palace" вместе с предложением двух отелей, в названии которых содержится "pa"?

    ![Ввод со встроенным автозаполнением и предложениями](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Попробуйте выполнить переход, чтобы принять предложение автозаполнения, а также попробуйте выбрать предложения, используя клавиши со стрелками и клавишу Tab, и повторите попытку с помощью одного щелчка мышью. Убедитесь, что скрипт работает должным образом во всех этих ситуациях.

    Возможно, вы решите, что для вас будет проще загрузить библиотеку с соответствующим функционалом. Но теперь вы знаете как минимум один способ добавить функцию встроенного автозаполнения.

## <a name="takeaways"></a>Общие выводы

По результатам этого проекта можно сделать такие выводы:

* Функции автозаполнения (также называется "упреждающий ввод") и предложений позволяют пользователю найти то, что нужно, введя лишь несколько ключевых символов.
* Совместное использование функций автозаполнения и предложений обеспечивает улучшенные возможности для пользователя.
* Необходимо всегда тестировать функции автозаполнения со всеми формами ввода.
* Функция **setInterval** может оказаться полезной при проверке и корректировке элементов пользовательского интерфейса.

## <a name="next-steps"></a>Дальнейшие действия

Из следующего руководства мы узнаем еще один способ улучшения возможностей для пользователя: использование аспектов для сужения области поиска одним щелчком.

> [!div class="nextstepaction"]
> [Руководство для разработчиков C#. Использование аспектов для улучшения навигации в службе "Когнитивный поиск Azure"](tutorial-csharp-facets.md)