---
title: Добавление автозаполнения и предложений в поле поиска
titleSuffix: Azure Cognitive Search
description: Включите действия запроса поиска по мере ввода в свой тип в Azure Cognitive Search, создавая предложить и формулируя запросы, которые автоматически заполняют окно поиска с готовыми терминами или фразами. Вы также можете вернуть предлагаемые матчи.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758111"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Добавление автозаполнения и предложений в клиентские приложения

Поиск по типу является распространенным методом повышения производительности запросов, инициированных пользователями. В Azure Cognitive Search этот опыт поддерживается с помощью *автозаполнения,* который завершает термин или фразу, основанную на частичном входе (завершение "микро" с "microsoft"). Другая форма *предложения*: краткий список соответствующих документов (возвращение книги с ID, так что вы можете ссылку на страницу подробно). Как автозаполнение, так и предложения основаны на совпадении индекса. Служба не будет предлагать запросы, которые возвращают нулевые результаты.

Для реализации этих возможностей в Azure Cognitive Search вам потребуется:

+ *Наводящий* на задний конец.
+ *Запрос* с указанием [API Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) или [Предложений](https://docs.microsoft.com/rest/api/searchservice/suggestions) по запросу.
+ *Элемент управления системой управления взаимодействием* в клиентском приложении для обработки взаимодействий по мере использования. Для этого мы рекомендуем использовать существующую библиотеку JavaScript.

В Azure Cognitive Search автозавершенные запросы и предлагаемые результаты извлекаются из индекса поиска из выбранных полей, зарегистрированных у предполагаемого. Наводящий является частью индекса, и он определяет, какие поля будут предоставлять содержимое, которое либо завершает запрос, предлагает результат или делает и то, и другое. При создании и загрузке индекса внутренняя структура данных для хранения префиксов, используемых для сопоставления по частичным запросам, создается структура данных. Для предложений, выбирая подходящие поля, которые являются уникальными, или, по крайней мере, не повторяющиеся, имеет важное значение для опыта. Для получения дополнительной информации [см.](index-add-suggesters.md)

Остальная часть этой статьи сосредоточена на запросах и клиентском коде. Для иллюстрации ключевых моментов используются JavaScript и C-для иллюстрации ключевых моментов. Примеры REST API используются для краткого представления каждой операции. Для ссылок на сквозные образцы кода [см.](#next-steps)

## <a name="set-up-a-request"></a>Настройка запроса

Элементы запроса включают один из AIS типа поиска по типу, частичный запрос и наводящий. Следующий скрипт иллюстрирует компоненты запроса, используя в качестве примера Autocomplete REST API.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName** дает вам наводящий осведомлены поля, используемые для завершения терминов или предложений. Что касается предложений, то, в частности, перечень полевых работ должен состоять из тех, которые предлагают четкий выбор соответствующих результатов. На сайте, который продает компьютерные игры, поле может быть название игры.

Параметр **поиска** обеспечивает частичный запрос, в котором символы подаются в запрос запроса через автополный элемент управления j'sry. В приведенном выше примере "минекраф" является статической иллюстрацией того, что контроль мог бы пройти дюйма

AA не устанавливают минимальные требования к длине частичного запроса; это может быть всего один символ. Тем не менее, J''s Autocomplete обеспечивает минимальную длину. Типично как минимум два или три символа.

Матчи находятся в начале термина в любом месте ввода строки. Учитывая "быструю коричневую лису", как автозаполнение, так и предложения будут совпадать по частичным версиям "", "быстрый", "коричневый", или "лиса", но не на частичных infix терминах, таких как "rown" или "ox". Кроме того, каждый матч устанавливает возможности для расширения вниз по течению. Частичный запрос "быстрый br" будет соответствовать на "быстрый коричневый" или "быстрый хлеб", но ни "коричневый" или "хлеб" сами по себе не будет соответствовать, если "быстрый" предшествует им.

### <a name="apis-for-search-as-you-type"></a>AA для поиска по мере ввода

Следите за этими ссылками на справочных страницах REST и .NET SDK:

+ [Предложения REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Автополный REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Предложитьметодhttpсообщенияасасинки](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Метод AutocompleteWithMessagesasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Структура ответа

Ответы на автозаполнение и предложения — это то, что можно ожидать для шаблона: [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) возвращает список терминов, [Предложения](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) возвращает термины плюс идентификатор документа, чтобы вы могли получить документ (используйте API [документа lookup](https://docs.microsoft.com/rest/api/searchservice/lookup-document) для получения конкретного документа для подробной страницы).

Ответы формируются параметрами запроса. Для Autocomplete установите [**autocompleteMode,**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) чтобы определить, происходит ли завершение текста на одном или двух сроках. Для предложений выберете поле, определяющее содержание ответа.

Для предложений следует дополнительно уточнить ответ, чтобы избежать дубликатов или, как представляется, не связанных результатов. Чтобы контролировать результаты, включите больше параметров в запросе. Следующие параметры применяются как к автозаполнению, так и к предложениям, но, возможно, более необходимы для предложений, особенно когда предложитель включает в себя несколько полей.

| Параметр | Использование |
|-----------|-------|
| **$select** | Если у вас есть несколько **sourceFields** в предложить, используйте`$select=GameTitle` **$select,** чтобы выбрать, какое поле способствует значения (). |
| **searchFields** | Ограничивайте запрос определенными полями. |
| **$filter** | Применить критерии соответствия`$filter=Category eq 'ActionAdventure'`на набор результатов ( ). |
| **$top** | Ограничьте результаты определенным`$top=5`числом ().|

## <a name="add-user-interaction-code"></a>Добавление кода взаимодействия пользователя

Автоматическое заполнение термина запроса или удаление списка соответствующих ссылок требует кода взаимодействия пользователя, обычно JavaScript, который может потреблять запросы из внешних источников, таких как автозаполнение или запросы предложений против когнитивного индекса Azure Search Cognitive.

Хотя этот код можно написать нативной информации, гораздо проще использовать функции из существующей библиотеки JavaScript. Эта статья демонстрирует два, один для предложений, а другой для автозаполнения. 

+ [Автополный виджет (J'ury UI)](https://jqueryui.com/autocomplete/) используется в примере предложения. Можно создать окно поиска, а затем ссылку на него в функции JavaScript, использующем виджет Autocomplete. Свойства на виджете устанавливают источник (функция автозаполнения или предложения), минимальную длину входных символов до принятия действия и позиционирование.

+ В примере автозаполнения используется модуль [XDSoft Autocomplete.](https://xdsoft.net/jqplugins/autocomplete/)

С помощью этих библиотек мы создаем поле поиска, которое поддерживает обе функции — предложения и автозаполнение. Входы, собранные в поле поиска, в паре с предложениями и действиями автозаполнения.

## <a name="suggestions"></a>Предложения

Этот раздел провенет вас через осуществление предложенных результатов, начиная с определения окна поиска. Он также показывает, как и скрипт, который вызывает первый JavaScript автоматической библиотеки, упомянутые в этой статье.

### <a name="create-a-search-box"></a>Создание окна поиска

Предполагая, что [автозаполненная библиотека j'uery UI](https://jqueryui.com/autocomplete/) и проект MVC в C, можно определить поле поиска с помощью JavaScript в файле **Index.cshtml.** Библиотека добавляет взаимодействие поиска по мере ввода в поле поиска, делая асинхронные вызовы контроллеру MVC для получения предложений.

В **Index.cshtml** под папкой «Виды»Home строка для создания окна поиска может быть следующей:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

В этом примере создается простое текстовое поле ввода с классом для определения стиля, идентификатором для ссылок на JavaScript и замещающим текстом.  

В том же файле встраивается JavaScript, который ссылается на поле поиска. Следующая функция вызывает API предложения, который запрашивает предложил соответствующие документы на основе частичных входов термина:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Функция `source` автозаполнения j'u'ry UI, где можно получить список элементов для отображания под полем поиска. Поскольку этот проект является проектом MVC, он называет функцию **Suggest** в **HomeController.cs** которая содержит логику для возврата предложений запроса. Также эта функция передает несколько параметров для управления выделением, нечетким соответствием и терминами. API JavaScript автозаполнения добавляет параметр термина.

Рекомендации `minLength: 3` будут отображаться только при наличии по крайней мере трех символов в поле поиска.

### <a name="enable-fuzzy-matching"></a>Включить нечеткое соответствие

Поиск нечетких соответствий позволяет получить результаты, даже если пользователь неверно напишет слово в поле поиска. Расстояние редактирования 1, что означает, что может быть максимальное расхождение одного символа между ввозам пользователя и совпадением. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Включить выделение

Выделение применяет стиль шрифта к символам в результате, который соответствует вхвому. Например, если частичный ввод является "микро", результат будет отображаться как **микромягкий,** **микро-область**и так далее. Выделение основано на параметрах HighlightPreTag и HighlightPostTag, определенных в соответствии с функцией предложения.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Предложить функцию

Если вы используете приложение C-и MVC, **HomeController.cs** файл в каталоге контроллеров — это место, где можно создать класс для предлагаемых результатов. В .NET функция Suggest основана на [методе DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

Метод `InitSearch` создает аутентифицированный клиент индекса HTTP для службы когнитивного поиска Azure. Для получения дополнительной информации о .NET SDK смотрите [как использовать Azure Cognitive Search из приложения .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
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

Функция предложений использует два параметра, которые определяют, возвращается ли четкое совпадение или используется нечеткое соответствие в дополнение к вводу слова для поиска. Метод создает [объект SuggestParameters,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)который затем передается aPI Suggest API. Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

## <a name="autocomplete"></a>Автозавершение

До сих пор код поиска UX был сосредоточен на предложениях. Следующий блок кода показывает автозаполнение, используя функцию автозаполнения запроса XDSoft j'uery, проходя запрос на автозаполнение Azure Cognitive Search. Как и в том, что в приложении сc-код, поддерживающий взаимодействие с пользователем, входит в **index.cshtml.**

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Функция автозаполнения

Автозаполнение основано на [методе DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Как и в том, что этот блок кода будет идти в **HomeController.cs** файл.

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Эта функция автозавершения принимает входные данные с термином для поиска. Затем этот метод создает объект [AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Затем результат преобразуется в JSON, чтобы его можно было передать клиенту.

## <a name="next-steps"></a>Следующие шаги

Следуйте этим ссылкам для сквозных инструкций или кода, демонстрирующего опыт поиска по мере ввода. Оба примера кода включают гибридные реализации предложений и автозаполнение вместе.

+ [Учебник: Создайте свое первое приложение в СЗ (урок 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Образец кода СК: лазурный поиск-дотнет-образцы/создание первого приложения/3-добавить-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [Си-я и JavaScript с образцом кода REST](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)