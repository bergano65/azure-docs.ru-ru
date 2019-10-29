---
title: Добавление предложений и автозаполнения в поле поиска
titleSuffix: Azure Cognitive Search
description: Включите действия опережающего ввода запросов в Когнитивном поиске Azure, создав средства подбора и составив запросы, которые предлагают завершенные термины или фразы в поле поиска.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792528"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Добавление предложений или автозаполнение в приложениях Когнитивного поиска Azure

Из этой статьи вы узнаете, как использовать [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [автозаполнение](https://docs.microsoft.com/rest/api/searchservice/autocomplete), чтобы дополнить возможности поля поиска функциями поиска по мере ввода.

+ *Предложения* — это предлагаемые результаты, которые создаются по мере ввода запроса. Каждое предложение содержит один результат из индекса, который соответствует введенному на данный момент тексту. 

+ *Автозаполнение* "завершает" набираемое пользователем слово или фразу. Эта функция не возвращает результаты, а завершает текст запроса, который затем можно выполнить для получения результатов. Как и с предложениями, завершенное слово или фраза в запросе прогнозируется по совпадениям из индекса. Эта служба не предлагает запросы, по которым в индексе не будет найдено результатов.

Вы можете скачать и запустить пример кода **DotNetHowToAutocomplete**, чтобы оценить эти функции. Этот пример кода обращается к предварительно созданному индексу, заполненном [демонстрационными данным NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Индекс NYCJobs содержит [конструкцию средства подбора](index-add-suggesters.md), что является обязательным условием для использования предложений и (или) автозаполнения. Вы можете использовать уже подготовленный индекс, размещенный в службе песочницы, или [заполнить собственный индекс](#configure-app) с помощью загрузчика данных из примера решения NYCJobs. 

В примере **DotNetHowToAutocomplete** демонстрируются функции предложения и автозаполнения на языках C# и JavaScript. Разработчики C# могут пошагово выполнить приложение на основе ASP.NET MVC, которое использует[пакет SDK .NET для Когнитивного поиска Azure](https://aka.ms/search-sdk). Логику для вызова автозаполнения и предложений можно найти в файле HomeController.cs. Для разработчиков JavaScript аналогичная логика представлена в файле IndexJavaScript.cshtml, где содержатся прямые вызовы [REST API Когнитивного поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Интерфейс пользователя для версий на обоих языках создан на основе [пользовательского интерфейса jQuery](https://jqueryui.com/autocomplete/) и библиотек [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). С помощью этих библиотек мы создаем поле поиска, которое поддерживает обе функции — предложения и автозаполнение. Входные данные, собранные в поле поиска, сопоставляются с действиями предложений и автозаполнения, которые в нашем примере размещены в файлах HomeController.cs и IndexJavaScript.cshtml.

В этом упражнении вы выполните следующие действия:

> [!div class="checklist"]
> * Реализация поля для ввода поискового запроса на JavaScript и отправка запросов для получения предложений или автозаполняемых терминов.
> * В C# определите действия предложений и автозаполнения в HomeController.cs.
> * В JavaScript воспользуетесь прямым вызовом интерфейсов REST API, который предоставляет те же возможности.

## <a name="prerequisites"></a>Предварительные требования

Служба "Когнитивный поиск Azure" не является обязательной для этого упражнения, так как решение использует службу песочницы с заранее подготовленным демонстрационным индексом NYCJobs. Если вы хотите выполнить этот пример в собственной службе поиска, воспользуйтесь инструкциями из статьи [о настройке индекса NYCJobs](#configure-app).

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (любой выпуск). Пример кода и инструкции были протестированы с помощью бесплатного выпуска Community Edition.

* Скачайте пример [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

В этом исчерпывающем примере демонстрируются функции предложения, автозаполнения, навигации с аспектами и кэширования на стороне клиента. Ознакомьтесь с файлом сведений и комментариями, чтобы получить полное описание работы этого примера.

## <a name="run-the-sample"></a>Запуск примера

1. Откройте **AutocompleteTutorial.sln** в Visual Studio. Это решение содержит проект ASP.NET MVC с подключением к демонстрационному индексу NYCJobs.

2. Нажмите клавишу F5, чтобы запустить проект и загрузить страницу в браузере по выбору.

В верхней части вы увидите параметр для выбора C# или JavaScript. В версии на C# вызывается HomeController из браузера и используется пакет SDK для .NET Когнитивного поиска Azure для получения результатов. 

В версии на JavaScript вызывается REST API Когнитивного поиска Azure непосредственно из браузера. Этот параметр обычно гораздо производительнее, так как контроллер не участвует в процессе. Можно выбрать параметр, который подходит для ваших потребностей и языковых параметров. На странице есть несколько примеров автозаполнения с инструкциями для каждого. Для каждого примера есть рекомендуемый пример текста.  

Попробуйте ввести несколько букв в каждом поле поиска и посмотрите, что произойдет.

## <a name="search-box"></a>Поле поиска

Для обеих версий на C# и JavaScript поле поиска реализовано одинаково. 

Откройте файл **Index.cshtml** из папки \Views\Home, где содержится такой код:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

В этом примере создается простое текстовое поле ввода с классом для определения стиля, идентификатором для ссылок на JavaScript и замещающим текстом.  Вся функциональность реализована во внедренном коде JavaScript.

В примере на языке C# используется JavaScript в Index.cshtml для применения [библиотеки автозавершения пользовательского интерфейса jQuery](https://jqueryui.com/autocomplete/). Эта библиотека расширяет возможности автозавершения в поле поиска, совершая асинхронные вызовы котроллера MVC для получения предложений. Версия на языке JavaScript находится в файле IndexJavaScript.cshtml. Она содержит приведенный ниже скрипт для панели поиска, а также вызовы к REST API Когнитивного поиска Azure.

Давайте изучим код JavaScript из первого примера, который вызывает функцию автозавершения в интерфейсе jQuery, передавая запрос на предоставление предложений:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Представленный выше код выполняется в браузере при загрузке страницы и настраивает автозавершения в пользовательском интерфейсе JQuery для поля ввода example1a.  `minLength: 3` гарантирует, что рекомендации будут отображаться, только когда пользователь введет в поле поиска хотя бы три символа.  Исходное значение является важным:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Представленная выше строка указывает функции автозавершения в пользовательском интерфейсе JQuery, где можно получить список элементов для отображения под полем поиска. Поскольку этот пример является проектом MVC, он вызывает функцию Suggest из файла HomeController.cs, которая содержит логику предоставления предложений по запросам (Suggest подробнее описана в следующем разделе). Также эта функция передает несколько параметров для управления выделением, нечетким соответствием и терминами. API JavaScript автозаполнения добавляет параметр термина.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Расширение примера для поддержки нечеткого соответствия

Поиск нечетких соответствий позволяет получить результаты, даже если пользователь неверно напишет слово в поле поиска. Эта возможность не является необходимой, но значительно повышает надежность интерфейса с упреждающим вводом. Давайте испытаем это, изменив исходную строку, чтобы использовать нечеткое соответствие.

Измените такой вариант строки:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

на такой:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Запустите приложение, нажав клавишу F5.

Попробуйте ввести "деректор" и обратите внимание, что возвращаются результаты для запроса "директор", хотя это не точное совпадение.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>Аавтозавершение jQuery на основе автозавершения Когнитивного поиска Azure

До сих пор весь код интерфейса поиска был посвящен функции предложений. В следующем блоке кода демонстрируется функция автозавершения (строка 91 в файле index.cshtml) из пользовательского интерфейса JQuery, которая передает запрос на автозавершение в Когнитивный поиск Azure.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Пример C#

Итак, мы изучили код JavaScript для веб-страницы и готовы перейти к коду серверного контроллера на C#, который извлекает предложенные совпадения с помощью пакета SDK .NET для Когнитивного поиска Azure.

Откройте файл **HomeController.cs** из каталога Controllers. 

Вы наверняка сразу заметите метод в верхней части класса с именем `InitSearch`. Этот метод создает клиент индекса HTTP с проверкой подлинности в службе "Когнитивный поиск Azure". Дополнительные сведения см. в статье [об использовании Когнитивного поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

В строке 41 приводится функция Suggest. Она основана на методе [DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Функция предложений использует два параметра, которые определяют, возвращается ли четкое совпадение или используется нечеткое соответствие в дополнение к вводу слова для поиска. Этот метод создает объект [SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), который затем передается в API предложений. Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

В строке 69 приводится функция Autocomplete. Она основана на методе [DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Эта функция автозавершения принимает входные данные с термином для поиска. Затем этот метод создает объект [AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

Добавьте точку останова в начале функции предложений и выполните пошаговую отладку кода (необязательно). Обратите внимание на ответ, возвращаемый пакетом SDK, и его преобразование в результат, возвращаемый методом.

Другие примеры на этой странице следуют той же схеме, добавляя выделение совпадений и аспекты для поддержки кэширования результатов автозавершения на стороне клиента. Просмотрите каждый из них и разберитесь, как они работают и как использовать их при поиске.

## <a name="javascript-example"></a>Пример JavaScript

Реализация автозавершения и предложений на JavaScript вызывает REST API, используя в качестве источника универсальный код ресурса (URI), который определяет индекс и операцию. 

Чтобы ознакомиться с реализацией на JavaScript, откройте файл **IndexJavaScript.cshtml**. Обратите внимание, что функция автозавершения в пользовательском интерфейсе jQuery также используется для поля поиска, сбора терминов для поиска и выполнения асинхронных вызовов Когнитивного поиска Azure для получения предложенных совпадений или завершенных терминов. 

Давайте взглянем на код JavaScript в первом примере:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Если сравнить этот пример с примером выше, который вызывает контроллер Home, вы заметите несколько сходств.  Конфигурации автозаполнения для `minLength` и `position` абсолютно одинаковы. 

А вот источник значительно отличается. Вместо вызова метода Suggest из контроллера Home в функции JavaScript создается запрос REST и выполняется по технологии Ajax. Затем ответ обрабатываются в success и используется в качестве источника.

В вызовах REST используется URI, чтобы различать вызовы API для функций [автозавершения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) и [предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions). Следующие URI находятся в строках 9 и 10, соответственно.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

В строке 148 есть скрипт, который вызывает `autocompleteUri`. Первый вызов `suggestUri` находится в строке 39.

> [!Note]
> Выполнение вызовов REST к службе из JavaScript здесь реализовано только для демонстрации возможностей REST API, и не может считаться рекомендацией или современной технологией. Добавление ключа API и конечной точки в скрипт открывает возможность для атак типа "отказ в обслуживании" каждому, кто может считывать эти значения из скрипта. Такой код JavaScript можно безопасно использовать в целях обучения, например с индексами в бесплатной службе, но мы рекомендуем всегда предпочитать Java или C# для операций индексирования и запросов в рабочем коде. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Настройка NYCJobs для запуска в службе

До настоящего момента вы использовали заранее размещенный демонстрационный индекс NYCJobs. Если вы хотите полностью контролировать весь код, включая код индекса, выполните следующие инструкции по созданию индекса и его отправке в службу поиска.

1. [Создайте службу "Когнитивный поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать для этого примера бесплатную службу. 

   > [!Note]
   > В бесплатной версии службы "Когнитивный поиск Azure" вы можете использовать не более трех индексов. Загрузчик данных NYCJobs создает два индекса. Убедитесь, что у вас достаточно места, чтобы служба могла принять новые индексы.

1. Скачайте пример кода [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

1. В папке DataLoader с кодом примера NYCJobs найдите файл **DataLoader.sln** и откройте его в Visual Studio.

1. Добавьте сведения о подключении к используемой службе "Когнитивный поиск Azure". Откройте файл App.config в проекте DataLoader и измените параметры appSettings TargetSearchServiceName и TargetSearchServiceApiKey, чтобы они соответствовали службе "Когнитивный поиск Azure" и ключу API этой службы. Эти сведения можно найти на портале Azure.

1. Нажмите клавишу F5, чтобы запустить приложение, которое создаст два индекса и импортирует пример данных NYCJobs.

1. Откройте **AutocompleteTutorial.sln** и измените файл Web.config в проекте **AutocompleteTutorial**. Измените значения SearchServiceName и SearchServiceApiKey на допустимые значения для вашей службы поиска.

1. Нажмите клавишу F5 для запуска приложения. Пример веб-приложения откроется в браузере по умолчанию. У него такой же интерфейс, как и у версии для песочницы, но теперь индекс и данные размещаются в вашей службе.

## <a name="next-steps"></a>Дополнительная информация

Этот пример демонстрирует основные шаги по созданию поля поиска, которое поддерживает автозавершения и предложения. Вы узнали, как можно создать приложение ASP.NET MVC и использовать REST API или пакет SDK .NET для Когнитивного поиска Azure, чтобы получать предложения.

На следующем шаге вы интегрируете возможности предложений и автозавершения в интерфейс поиска. В этом вам помогут следующие статьи справочной документации.

> [!div class="nextstepaction"]
> [REST API автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Атрибут индекса аспектов в REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index)

