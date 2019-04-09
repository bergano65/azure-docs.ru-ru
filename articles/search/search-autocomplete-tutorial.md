---
title: Добавление предложения и автозаполнения в поле поиска — поиска Azure
description: Включите typeahead действия запроса в службе поиска Azure, создав средства подбора и составления запросов, заполните поле поиска с завершенной термины или фразы.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 43d289f2688bbf4927ee244d6ae9992782bf380e
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009824"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Пример: Добавление предложения или автозаполнения в приложение службы поиска Azure

В этой статье сведения об использовании [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) для создания мощных функций поиска поле, поддерживающее поведения поиска как то type.

+ *Предложения* , предлагаемые результаты, создаваемые при вводе, где каждое из них — единственный результат, возвращаемый индекс, который совпадает с набранным данный момент. 

+ *Автозаполнение*, [функцию предварительного просмотра](search-api-preview.md), «завершения», слово или фразу, пользователь вводит текст в настоящее время. Вместо возвращения результатов, оно завершает запрос, который затем можно выполнить для возвращения результатов. Как и в случае с предложениями, завершенного слова или фразы в запросе объявлено в совпадение в индексе. Службы не предлагают запросов, возвращающих нулевого результата в индексе.

Можно загрузить и запустить пример кода **DotNetHowToAutocomplete** для ознакомления с этими функциями. Пример кода предназначен для предварительно созданных индекса, заполненный [NYCJobs демонстрационные данные](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Содержит индекс NYCJobs [средство подбора конструкция](index-add-suggesters.md), что является требованием для использования предложения или автозаполнения. Можно использовать индекс подготовленного, размещенных в службу "песочницы", или [заполнения собственных индекса](#configure-app) с помощью загрузчика данных в примере решения NYCJobs. 

**DotNetHowToAutocomplete** в нем демонстрируется предложения и автозаполнения в обоих C# и версиях языка JavaScript. C#разработчикам можно пошагово выполнить приложение на основе ASP.NET MVC, использующее [пакет SDK Azure Search .NET](https://aka.ms/search-sdk). Логика для автозаполнения, а также вызовы предложенных запросов можно найти в файле HomeController.cs. Разработчики JavaScript найдет логики эквивалентный запрос в IndexJavaScript.cshtml, включая прямые вызовы [API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Для обеих версий языка на основе взаимодействия с пользователем интерфейсный [пользовательский Интерфейс jQuery](https://jqueryui.com/autocomplete/) и [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) библиотеки. Мы используем эти библиотеки для построения поле поиска, поддержка, предложения и автозаполнения. Входные данные, собранные в поле поиска сопоставляются с предложения и автозаполнения действия, такие, как определено в HomeController.cs или IndexJavaScript.cshtml.

В этом упражнении рассматриваются следующие задачи:

> [!div class="checklist"]
> * Реализовать поисковое поле ввода в JavaScript для передачи запросов для предлагаемых совпадений или условия автозаполнения
> * В C#, определите предложения и действия автозаполнения в HomeController.cs
> * В JavaScript вызовите REST API напрямую для сохранения функциональности

## <a name="prerequisites"></a>Технические условия

В службе поиска Azure является необязательным для этого упражнения, так как решение использует службу live "песочницы", размещение подготовленного индекс демоверсии NYCJobs. Если вы хотите выполнить этот пример в службе поиска, см. в разделе [индекса вакансий Нью-ЙОРКА Настройка](#configure-app) инструкции.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (любой выпуск). Пример кода и инструкции были протестированы с помощью бесплатного выпуска Community Edition.

* Скачайте [DotNetHowToAutoComplete пример](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Образец представляет комплексное покрытия, автозаполнения, фасетная навигация и предложения кэширование на стороне клиента. Изучите файл readme и комментарии для полного описания образец предлагает.

## <a name="run-the-sample"></a>Запуск примера

1. Откройте **AutocompleteTutorial.sln** в Visual Studio. Решение содержит проект ASP.NET MVC с подключением к индекса Демонстрация вакансий Нью-ЙОРКА.

2. Нажмите клавишу F5, чтобы запустить проект и загрузить страницу в браузере по выбору.

В верхней части вы увидите параметр для выбора C# или JavaScript. C# Параметр вызовы HomeController из браузера и использует пакет SDK для .NET для поиска Azure для получения результатов. 

Параметр JavaScript вызывает REST API службы поиска Azure непосредственно из браузера. Этот параметр обычно гораздо производительнее, так как контроллер не участвует в процессе. Можно выбрать параметр, который подходит для ваших потребностей и языковых параметров. На странице есть несколько примеров автозаполнения с инструкциями для каждого. Для каждого примера есть рекомендуемый пример текста.  

Попробуйте ввести несколько букв в каждом поле поиска и посмотрите, что произойдет.

## <a name="search-box"></a>Поле поиска

Для обоих C# и версии JavaScript, реализацию поле поиска остается прежним. 

Откройте **Index.cshtml** файл \Views\Home папки для просмотра кода:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Это простое текстовое поле ввода с классом для стилей, идентификатор для ссылки на JavaScript и замещающий текст.  Магия имеет встроенный JavaScript.

C# Пример кода на языке использует JavaScript в файле Index.cshtml использовать [библиотеки автозаполнения пользовательского интерфейса jQuery](https://jqueryui.com/autocomplete/). Эта библиотека расширяет возможности автозавершения в поле поиска, асинхронных вызовах контроллер MVC для получения предложения. Версия языка JavaScript, IndexJavaScript.cshtml. Он содержит приведенный ниже сценарий для панели поиска, а также вызовы REST API службы поиска Azure.

Давайте взглянем на код JavaScript для первого примера, который вызывает jQuery функция автозаполнения пользовательского интерфейса, передавая в запросе предложения:

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

Приведенный выше код выполняется в браузере при загрузке страницы для настройки пользовательского интерфейса jQuery Автозаполнение поле ввода «example1a».  `minLength: 3` гарантирует, что рекомендации будут отображены только при наличии по крайней мере три символа в поле поиска.  Исходное значение является важным:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Приведенную выше строку сообщает функция автозаполнения пользовательского интерфейса jQuery, где можно получить список элементов для отображения под полем поиска. Так как это проекта MVC, он вызывает функцию предложить в HomeController.cs, который содержит логику для возвращения предложений запроса (Подробнее о предложить в следующем разделе). Эта функция также передает ряд параметров выделения элемента управления, нечеткое соответствие и термин. API JavaScript автозаполнения добавляет параметр термина.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Расширение примера для поддержки нечеткого соответствия

Поиск нечетких соответствий позволяет получить результаты, даже если пользователь неверно напишет слово в поле поиска. Не обязательно, это значительно улучшает надежность работы typeahead. Давайте испытаем это, изменив исходную строку, чтобы использовать нечеткое соответствие.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery автозаполнения, поддерживаемый автозаполнения поиска Azure

На данный момент поиска UX-код были по центру на предложения. Следующий блок кода показано, функция автозаполнения пользовательского интерфейса jQuery (строки 91 в index.cshtml), передавая в запросе для автозаполнения поиска Azure:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
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

Теперь, когда мы изучили код JavaScript для веб-страницы, давайте взглянем на C# контроллера серверный код, который фактически получает предлагаться варианты, с помощью пакета SDK для .NET для поиска Azure.

Откройте **HomeController.cs** файл в каталог контроллеров. 

Первое, что вы можете заметить, — это метод, в верхней части класса, который вызывается `InitSearch`. Он создает прошедший проверку подлинности клиент индекса HTTP для службы поиска Azure. Дополнительные сведения см. в разделе [использование поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

В строке 41 Обратите внимание, что функция предложить. Он основан на [DocumentsOperationsExtensions.Suggest метод](https://docs.microsoft.com/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

Функция предложений использует два параметра, которые определяют, возвращается ли четкое совпадение или используется нечеткое соответствие в дополнение к вводу слова для поиска. Этот метод создает [SuggestParameters объекта](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), который затем передается в API предложить. Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

В строке 69 Обратите внимание, что функция автозаполнения. Он основан на [DocumentsOperationsExtensions.Autocomplete метод](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

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

Функция автозаполнения принимает входные данные для поиска терминов. Этот метод создает [AutoCompleteParameters объект](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

Добавьте точку останова в начале функции предложений и выполните пошаговую отладку кода (необязательно). Обратите внимание, что ответ, возвращаемый пакетом SDK, а также каким образом он преобразуется в результат, возвращенный из метода.

Другие примеры на странице следуйте же шаблон, чтобы добавить выделение совпадений и аспекты для поддержки клиентского кэширования результатов автозаполнения. Просмотрите каждый из них и разберитесь, как они работают и как использовать их при поиске.

## <a name="javascript-example"></a>Пример JavaScript

Реализация Javascript автозаполнения и предложения вызывает REST API, используя URI в качестве источника для указания индекса и операции. 

Чтобы просмотреть реализацию JavaScript, откройте **IndexJavaScript.cshtml**. Обратите внимание, что функция автозаполнения пользовательского интерфейса jQuery также используется для поля поиска, сбор входных данных термин для поиска и выполнения асинхронных вызовов в службу поиска Azure для получения предложенный совпадений или завершения условия. 

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

Если сравнить это с примером выше, который вызывает контроллер Home, вы заметите несколько сходств.  Конфигурации автозаполнения для `minLength` и `position` абсолютно одинаковы. 

А вот источник значительно отличается. Вместо вызова метода предложить в контроллер home, запрос REST в функцию JavaScript создается и выполняется с помощью Ajax. Затем ответ обрабатываются в success и используется в качестве источника.

Вызовы REST использования URI для указания ли [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) или [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) вызов API. Следующие URI являются в строках 9 и 10 соответственно.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

В строке 148, можно найти скрипт, который вызывает `autocompleteUri`. Первый вызов `suggestUri` на строки 39.

> [!Note]
> Вызовы REST к службе в JavaScript предоставляется здесь как удобный демонстрацию REST API, но не должны толковаться как рекомендации или рекомендация. Включение ключ API и конечной точки в скрипте открывает службу до всем, кто может их читать эти значения off скрипт атак отказа в обслуживании. Во время его безопасно ли использовать JavaScript в целях обучения, возможно по индексам, размещенных в бесплатной версии службы, мы рекомендуем использовать Java или C# для операций индексирования и выполнения запросов в рабочем коде. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Настройка NYCJobs для запуска службы

До сих пор вы уже использовали размещенной индекс демоверсии NYCJobs. Полную картину весь код, включая индекс, выполните эти инструкции, чтобы создать и загрузить индекс в службе поиска.

1. [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Можно использовать это бесплатная служба, в этом примере. 

   > [!Note]
   > В бесплатной версии службы поиска Azure вы можете использовать не более трех индексов. Загрузчик данных NYCJobs создает два индекса. Убедитесь, что у вас достаточно места, чтобы служба могла принять новые индексы.

1. Скачайте [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) пример кода.

1. В папке "загрузчик данных" NYCJobs примеров кода, откройте **DataLoader.sln** в Visual Studio.

1. Добавьте сведения о подключении для службы поиска Azure. Откройте файл App.config в проекте DataLoader и измените параметры appSettings TargetSearchServiceName и TargetSearchServiceApiKey, чтобы они соответствовали службе поиска Azure и ключу API службы поиска Azure. Они находятся на портале Azure.

1. Нажмите клавишу F5 для запуска приложения, создание двух индексов и Импорт демонстрационных данных NYCJob.

1. Откройте **AutocompleteTutorial.sln** и изменить файл Web.config в **AutocompleteTutorial** проекта. Измените значения SearchServiceName "и" SearchServiceApiKey на значения, допустимые для службы поиска.

1. Нажмите клавишу F5 для запуска приложения. Пример веб-приложения откроется в браузере по умолчанию. Интерфейс идентична версии "песочницы", только индекс и данные размещаются в службе.

## <a name="next-steps"></a>Дальнейшие действия

Этот пример демонстрирует основные шаги по созданию поле поиска, автозаполнения и предложения. Вы узнали, как можно создать приложение ASP.NET MVC и использовать пакет SDK Azure Search .NET или REST API для получения предложения.

На следующем шаге, попытки интеграции предложения и автозаполнения в возможностей поиска. Следующие справочные статьи должно помочь.

> [!div class="nextstepaction"]
> [REST API автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Атрибут индекса аспектов в REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index)

