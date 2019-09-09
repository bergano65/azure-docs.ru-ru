---
title: Добавление предложений и автозаполнения в поле поиска — Поиск Azure
description: Включите действия запроса typeahead в службе поиска Azure, создав средства подбора и запросы составления, которые заполняют поле поиска завершенными условиями или фразами.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183292"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Добавление предложений или автозаполнения в приложение поиска Azure

Из этой статьи вы узнаете, как использовать [предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions) и [Автозаполнение](https://docs.microsoft.com/rest/api/searchservice/autocomplete) для создания мощного поля поиска, поддерживающего поведение поиска по мере использования.

+ *Предложения* — предлагаемые результаты, создаваемые при вводе, где каждое предложение представляет собой один результат из индекса, который соответствует введенному на данный момент. 

+ *Автозаполнение* «завершает» слово или фразу, вводимую пользователем в данный момент. Вместо того чтобы возвращать результаты, он завершает запрос, который затем можно выполнить для возврата результатов. Как и в случае с предложениями, завершенное слово или фраза в запросе сопоставляется с совпадением в индексе. Служба не будет предлагать запросы, возвращающие нулевые результаты, в индексе.

Вы можете скачать и запустить пример кода в **дотнесовтоаутокомплете** , чтобы оценить эти функции. Образец кода предназначен для предварительно построенного индекса, заполненного [демонстрационными данными NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Индекс NYCJobs содержит [конструкцию предложения](index-add-suggesters.md), которая является требованием для использования предложений или автозаполнения. Можно использовать подготовленный индекс, размещенный в службе "песочницы", или [заполнить собственный индекс](#configure-app) с помощью загрузчика данных в образце решения NYCJobs. 

В образце **дотнесовтоаутокомплете** демонстрируются предложения и автозаполнение как C# в версии, так и на языке JavaScript. C#Разработчики могут пошагово пройти через приложение на основе ASP.NET MVC, которое использует [пакет SDK для .NET службы поиска Azure](https://aka.ms/search-sdk). Логика для выполнения автозаполнения и рекомендуемых вызовов запросов можно найти в файле HomeController.cs. Разработчики JavaScript найдут эквивалентную логику запросов в Индексжаваскрипт. cshtml, которая включает прямые вызовы [REST API поиска Azure](https://docs.microsoft.com/rest/api/searchservice/). 

Для обеих языковых версий интерфейс взаимодействия пользователя основан на библиотеках [UI jQuery](https://jqueryui.com/autocomplete/) и [ксдсофт](https://xdsoft.net/jqplugins/autocomplete/) . Мы используем эти библиотеки для создания поля поиска, поддерживающего как предложения, так и Автозаполнение. Входные данные, собранные в поле поиска, связаны с предложениями и действиями автозаполнения, такими как определенные в HomeController.cs или Индексжаваскрипт. cshtml.

Это упражнение поможет вам выполнить следующие задачи:

> [!div class="checklist"]
> * Реализация поля ввода поиска в JavaScript и выдача запросов для предложенных совпадений или автозаполненных терминов
> * В C#, определение предложений и действий автозаполнения в HomeController.CS
> * В JavaScript вызывайте интерфейсы API RESTFUL напрямую, чтобы предоставить те же функциональные возможности.

## <a name="prerequisites"></a>Предварительные требования

Служба поиска Azure является необязательной для этого упражнения, так как решение использует службу активной песочницы, в которой размещен подготовленный демонстрационный индекс NYCJobs. Если вы хотите выполнить этот пример в собственной службе поиска, см. инструкции в статье [Настройка индекса заданий Нью](#configure-app) .

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (любой выпуск). Пример кода и инструкции были протестированы с помощью бесплатного выпуска Community Edition.

* Скачайте [Пример дотнесовтоаутокомплете](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Этот пример является исчерпывающим, охватывающим предложения, автозаполнение, навигацию с аспектами и кэширование на стороне клиента. Ознакомьтесь с файлом readme и комментариями, чтобы получить полное описание образцов предложений.

## <a name="run-the-sample"></a>Запуск примера

1. Откройте **аутокомплететуториал. sln** в Visual Studio. Решение содержит проект ASP.NET MVC с подключением к демонстрационному индексу заданий Нью.

2. Нажмите клавишу F5, чтобы запустить проект и загрузить страницу в браузере по выбору.

В верхней части вы увидите параметр для выбора C# или JavaScript. C# Параметр вызывает HomeController из браузера и использует пакет SDK для .NET службы поиска Azure для получения результатов. 

Параметр JavaScript вызывает REST API службы поиска Azure непосредственно из браузера. Этот параметр обычно гораздо производительнее, так как контроллер не участвует в процессе. Можно выбрать параметр, который подходит для ваших потребностей и языковых параметров. На странице есть несколько примеров автозаполнения с инструкциями для каждого. Для каждого примера есть рекомендуемый пример текста.  

Попробуйте ввести несколько букв в каждом поле поиска и посмотрите, что произойдет.

## <a name="search-box"></a>Поле поиска

Для версий C# и JavaScript реализация поля поиска в точности одинакова. 

Откройте файл **index. cshtml** в папке \виевс\хоме, чтобы просмотреть код:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Этот пример представляет собой простое текстовое поле ввода с классом для стилей, ИДЕНТИФИКАТОРом, на который ссылается JavaScript, и текстом заполнителя.  Волшебность связана с внедренным кодом JavaScript.

Пример C# языка использует JavaScript в index. cshtml, чтобы использовать [библиотеку автозаполнения jQuery UI](https://jqueryui.com/autocomplete/). Эта библиотека добавляет интерфейс автозаполнения в поле поиска, делая асинхронные вызовы контроллера MVC для получения предложений. Версия языка JavaScript находится в Индексжаваскрипт. cshtml. Он включает в себя приведенный ниже скрипт для панели поиска, а также REST API вызовы поиска Azure.

Рассмотрим код JavaScript для первого примера, который вызывает функцию автозаполнения jQuery UI, передавая запрос на предоставление предложений:

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

Приведенный выше код выполняется в браузере при загрузке страницы, чтобы настроить автозаполнение пользовательского интерфейса jQuery для поля ввода "example1a".  `minLength: 3` гарантирует, что рекомендации будут отображаться, только когда пользователь введет в поле поиска хотя бы три символа.  Исходное значение является важным:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Приведенная выше строка сообщает функции автозаполнения jQuery UI, где можно получить список элементов, отображаемых под полем поиска. Поскольку этот проект является проектом MVC, он вызывает функцию предлагаю в HomeController.cs, которая содержит логику для возврата предложений запроса (Подробнее о предложении см. в следующем разделе). Эта функция также передает несколько параметров для управления выделением, нечетким соответствием и термином. API JavaScript автозаполнения добавляет параметр термина.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Расширение примера для поддержки нечеткого соответствия

Поиск нечетких соответствий позволяет получить результаты, даже если пользователь неверно напишет слово в поле поиска. Хотя это и не является обязательным, оно значительно повышает надежность typeahead. Давайте испытаем это, изменив исходную строку, чтобы использовать нечеткое соответствие.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Автозаполнение jQuery с помощью автозаполнения службы поиска Azure

До сих пор код UX поиска был выровнен по центру предложений. В следующем блоке кода показана функция автозавершения пользовательского интерфейса jQuery (строка 91 в index. cshtml), передающая запрос на Автозаполнение службы "Поиск Azure":

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

Теперь, когда мы проверили код JavaScript для веб-страницы, рассмотрим код контроллера на C# стороне сервера, который фактически извлекает предложенные соответствия с помощью пакета SDK для .NET службы поиска Azure.

Откройте файл **HomeController.CS** в каталоге Controllers. 

Первое, что вы можете заметить, — это метод в верхней части класса с именем `InitSearch`. Этот метод создает клиент индексов HTTP с проверкой подлинности для службы поиска Azure. Дополнительные сведения см. [в статье Использование службы поиска Azure в приложении .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

В строке 41 Обратите внимание на функцию предложить. Он основан на [методе документсоператионсекстенсионс. предлагаю](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Функция предложений использует два параметра, которые определяют, возвращается ли четкое совпадение или используется нечеткое соответствие в дополнение к вводу слова для поиска. Метод создает [объект сугжестпараметерс](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), который затем передается в API предложения. Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

В строке 69 Обратите внимание на функцию автозаполнения. Он основан на [методе документсоператионсекстенсионс. AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Функция автозаполнения принимает входные данные условия поиска. Метод создает [объект аутокомплетепараметерс](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

Добавьте точку останова в начале функции предложений и выполните пошаговую отладку кода (необязательно). Обратите внимание на ответ, возвращенный пакетом SDK, и его преобразование в результат, возвращенный методом.

Другие примеры на странице соответствуют одному и тому же шаблону, чтобы добавить выделение попаданий и аспекты для поддержки кэширования результатов автозаполнения на стороне клиента. Просмотрите каждый из них и разберитесь, как они работают и как использовать их при поиске.

## <a name="javascript-example"></a>Пример JavaScript

Реализация автозаполнения и предложений JavaScript вызывает REST API, используя универсальный код ресурса (URI) в качестве источника для указания индекса и операции. 

Чтобы ознакомиться с реализацией JavaScript, откройте **индексжаваскрипт. cshtml**. Обратите внимание, что функция автозаполнения в ИНТЕРФЕЙСе jQuery также используется для поля поиска, сбора входных данных терминов поиска и выполнения асинхронных вызовов службы поиска Azure для получения предложенных совпадений или завершенных условий. 

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

Если сравнить этот пример с примером выше, который вызывает контроллер Home, вы заметите несколько подобий.  Конфигурации автозаполнения для `minLength` и `position` абсолютно одинаковы. 

А вот источник значительно отличается. Вместо вызова метода предлагаю в контроллере Home в функции JavaScript создается запрос на ОСТАВШУЮся версия, который выполняется с помощью AJAX. Затем ответ обрабатываются в success и используется в качестве источника.

Вызовы функции RESTFUL используют URI, чтобы указать, выполняется ли вызов API [автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete) или [предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) . Следующие URI находятся в строках 9 и 10 соответственно.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

В строке 148 можно найти скрипт, который вызывает `autocompleteUri`. Первый вызов метода `suggestUri` находится в строке 39.

> [!Note]
> Создание вызовов RESTFUL к службе в JavaScript предоставляется здесь в качестве удобной демонстрации REST API, но не следует рассматривать как рекомендуемый подход или рекомендацию. Включение ключа API и конечной точки в скрипт открывает службу для атак типа "отказ в обслуживании" для всех, кто может считывать эти значения из скрипта. Хотя можно использовать JavaScript в целях обучения, возможно, на индексах, размещенных в бесплатной службе, мы рекомендуем использовать Java C# или для индексирования и выполнения операций запросов в рабочем коде. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Настройка NYCJobs для запуска в службе

До настоящего момента вы использовали демонстрационный индекс Hosted NYCJobs. Если вы хотите полностью просмотреть весь код, включая индекс, выполните эти инструкции, чтобы создать и загрузить индекс в свою службу поиска.

1. [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. В этом примере можно использовать бесплатную службу. 

   > [!Note]
   > В бесплатной версии службы поиска Azure вы можете использовать не более трех индексов. Загрузчик данных NYCJobs создает два индекса. Убедитесь, что у вас достаточно места, чтобы служба могла принять новые индексы.

1. Скачайте пример кода [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) .

1. В папке "Loader" образца кода NYCJobs откройте файл **Loader. sln** в Visual Studio.

1. Добавьте сведения о подключении для службы поиска Azure. Откройте файл App.config в проекте DataLoader и измените параметры appSettings TargetSearchServiceName и TargetSearchServiceApiKey, чтобы они соответствовали службе поиска Azure и ключу API службы поиска Azure. Эти сведения можно найти в портал Azure.

1. Нажмите клавишу F5, чтобы запустить приложение, создать два индекса и импортировать образец данных Никжоб.

1. Откройте **аутокомплететуториал. sln** и измените файл Web. config в проекте **аутокомплететуториал** . Измените значения SearchServiceName и SearchServiceApiKey на значения, допустимые для службы поиска.

1. Нажмите клавишу F5 для запуска приложения. Пример веб-приложения откроется в браузере по умолчанию. Работа аналогична версии "песочницы", в вашей службе размещаются только индекс и данные.

## <a name="next-steps"></a>Следующие шаги

В этом примере показаны основные шаги для создания поля поиска, поддерживающего Автозаполнение и предложения. Вы узнали, как создать приложение ASP.NET MVC и использовать пакет SDK .NET для поиска Azure или REST API для получения предложений.

В качестве следующего шага попытайтесь интегрировать предложения и автозаполнение в свой интерфейс поиска. Следующие справочные статьи помогут вам.

> [!div class="nextstepaction"]
> [REST API автозаполнения](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST API предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Атрибут индекса аспектов в REST API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-index)

