---
title: Руководство. Создание одностраничного веб-приложения с помощью API Bing для поиска изображений
titleSuffix: Azure cognitive services
description: API Bing для поиска изображений позволяет искать в Интернете соответствующие изображения высокого качества. Используйте это руководство для создания одностраничного веб-приложения, которое может отправлять поисковые запросы к API и отображать результаты на веб-странице.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 9/12/2018
ms.author: aahi
ms.openlocfilehash: 1c8e08cdf6238064e6ec2468a00bbc65ec46b675
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880755"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Руководство. Создание одностраничного веб-приложения с помощью API Bing для поиска изображений

API Bing для поиска изображений позволяет искать в Интернете соответствующие изображения высокого качества. Используйте это руководство для создания одностраничного веб-приложения, которое может отправлять поисковые запросы к API и отображать результаты на веб-странице. Это руководство аналогично [соответствующему руководству по](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) API Bing для поиска в Интернете.

На примере учебного приложения показано, как выполнить такие задачи:

> [!div class="checklist"]
> * Вызов API Bing для поиска изображений из JavaScript.
> * Улучшение результатов поиска с помощью параметров поиска.
> * Отображение и переход по страницам результатов поиска.
> * Запрос и обработка идентификатора клиента Bing и ключа подписки API.

Полный исходный код этого руководства доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Node.js](https://nodejs.org/).
* Платформа [Express.js](https://expressjs.com/) для Node.js. Инструкции по установке для исходного кода доступны в файле сведений примера на GitHub.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Управление ключами подписки пользователя и их хранение

Это приложение использует постоянное хранилище веб-браузера для хранения ключей подписки API. Если ключа нет в хранилище, веб-страница будет запрашивать у пользователя ключ и сохранит его для последующего использования. Если позже ключ будет отклонен API, то приложение удалит его из хранилища.


Определите функции `storeValue` и `retrieveValue`, которые используют объект `localStorage` (если браузер поддерживает его) или файл cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Функция `getSubscriptionKey()` пытается извлечь ранее сохраненный ключ с помощью `retrieveValue`. Если ключ не найден, пользователю будет предложено ввести его и сохранить с помощью `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

В теге HTML `<form>` атрибут `onsubmit` вызывает функцию `bingWebSearch`, чтобы возвращать результаты поиска. Функция `bingWebSearch` использует `getSubscriptionKey` для аутентификации каждого запроса. Как показано в предыдущем определении, `getSubscriptionKey` запрашивает у пользователя ключ, если он еще не был введен. Затем ключ сохраняется для последующего использования приложением.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Оправка поисковых запросов

Это приложение использует HTML-форму `<form>` для изначальной отправки пользовательских поисковых запросов с помощью атрибута `onsubmit` для вызова `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

По умолчанию обработчик `onsubmit` возвращает значение `false`, что препятствует отправке формы.

## <a name="select-search-options"></a>Выбор параметров поиска

![[Форма Bing для поиска изображений]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

API Bing для поиска изображений предоставляет несколько [параметров запроса фильтров](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#filter-query-parameters) для сужения и фильтрации результатов поиска. HTML-формы в этом приложении используют и отображают следующие параметры:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Раскрывающееся меню для выбора рынка (расположения и языка), который используется для поиска.                                                                                             |
| `query`      | Текстовое поле для ввода условий поиска.                                                                                                                                 |
| `aspect`     | Переключатели для выбора пропорций найденных изображений: примерно квадратные, вертикальные или горизонтальны.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Раскрывающееся меню для ограничения поиска результатами за последний день, неделю или месяц (необязательный параметр).                                                                                          |
| `safe`       | Флажок, указывающий, следует ли использовать функцию безопасного поиска Bing для фильтрации результатов с материалами для взрослых.                                                                                      |
| `count`      | Скрытое поле. Количество результатов поиска для каждого запроса. Измените значение, чтобы на странице отображалось меньше или больше результатов.                                                            |
| `offset`     | Скрытое поле. Смещение первого результата поиска в запросе. Используется для разбиения по страницам. Сбрасывается на `0` при новом запросе.                                                           |
| `nextoffset` | Скрытое поле. При получении результатов поиска для этого поля устанавливается значение `nextOffset` из ответа. Это поле позволяет избежать перекрывающихся результатов на соседних страницах. |
| `stack`      | Скрытое поле. Список смещений для предыдущих страниц результатов поиска в формате JSON. Используется для перехода на предыдущие страницы.                                                      |

Функция `bingSearchOptions()` форматирует эти параметры в частичную строку запроса, которую можно использовать в запросах API приложения.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Выполнение запроса

Используя поисковой запрос, строку параметров и ключ API, функция `BingImageSearch()` применяет объект XMLHttpRequest для выполнения запроса к конечной точке Поиска изображений Bing.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

После успешного выполнения HTTP-запроса JavaScript вызывает "загруженный" обработчик событий `handleBingResponse()`, чтобы передать успешный HTTP-запрос GET.

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Успешные HTTP-запросы могут содержать сведения о неудачном поиске. Если при выполнении поиска возникает ошибка, то API Bing для поиска изображений возвратит код состояния, отличный от "HTTP: 200", и сведения об ошибке в ответе JSON. Кроме того, если у запроса есть ограничение по скорости, то API возвратит пустой ответ.

## <a name="display-the-search-results"></a>Отображение результатов поиска

Результаты поиска отображаются в функции `renderSearchResults()`, которая принимает данные JSON, возвращаемые службой Поиск изображений Bing, и вызывает соответствующую функцию отрисовщика для любых возвращенных изображений и связанных поисковых запросов.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Результаты поиска изображений содержаться в объекте верхнего уровня `value` в ответе JSON. Они передаются в функцию `renderImageResults()`, которая просматривает все результаты и преобразует каждый элемент в формат HTML.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

API Bing для поиска изображений может возвращать четыре типа предложений поиска, которые помогают направлять пользовательский поиск, каждое из них в собственном объекте верхнего уровня:

| Предложение         | ОПИСАНИЕ                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Запросы, которые заменяют сводное слово в исходном поиске другим. Например, если вы ищете "красные цветы", сводным словом может быть "красные", а сводным предложением может быть "желтые цветы". |
| `queryExpansions`  | Запросы, которые сужают исходный поиск, добавляя больше условий. Например, если вы ищете "Microsoft Surface", расширением запроса может быть "Microsoft Surface Pro".                                   |
| `relatedSearches`  | Запросы, которые вводились другими пользователями, использовавшими ранее исходный поиск. Например, если вы ищете "гора Рейнир", может применяться связанный поиск "гора Святой Елены".                       |
| `similarTerms`     | Запросы, похожие по смыслу на исходный запрос. Например, если вы ищете "котята", аналогичным термином может быть "милота".                                                                   |

Это приложение преобразовывает для просмотра только предложения `relatedItems` и размещает результирующие ссылки на боковой панели страницы.

## <a name="rendering-search-results"></a>Преобразование для просмотра результатов поиска

В этом приложении есть объект `searchItemRenderers`, который содержит функции-отрисовщики, создающие HTML-код для каждого типа результатов поиска.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Эта функция-отрисовщик может принимать следующие параметры:

| Параметр         | ОПИСАНИЕ                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Объект JavaScript, содержащий свойства элемента, такие как URL-адрес и его описание. |
| `index` | Индекс элемента результата в коллекции.                                          |
| `count` | Число элементов в коллекции результатов поиска.                                  |

Параметры `index` и `count` используются для нумерации результатов, создания специального HTML-кода для коллекций и упорядочения содержимого. В частности, код:

* вычисляет размер эскиза изображения (переменная ширина — не менее 120 пикселей, и фиксированная высота — 90 пикселей);
* создает HTML-тег `<img>` для отображения эскиза изображения;
* создает HTML-теги `<a>` со ссылкой на изображение и страницей, которая его содержит;
* создает описание со сведениями об этом изображении и о сайте, на котором оно размещено.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Параметры `height` и `width` эскиза изображения указываются в теге `<img>` и в полях `h` и `w` в URL-адресе эскиза. Это позволяет Bing вернуть [эскиз](resize-and-crop-thumbnails.md) точно такого размера.

## <a name="persisting-client-id"></a>Сохранение идентификатора клиента

Ответы от интерфейсов API поиска Bing могут содержать заголовок `X-MSEdge-ClientID`, который необходимо отправить обратно в API с последующими запросами. Если используется несколько API-интерфейсов поиска Bing, то по возможности со всеми ими необходимо использовать один идентификатор клиента.

Наличие заголовка `X-MSEdge-ClientID` позволяет API-интерфейсам Bing связывать все поисковые запросы пользователя, что является полезным.

Во-первых, поисковая система Bing может применять к операциям поиска прошлый контекст и находить результаты, которые лучше соответствуют требованиям пользователя. Если пользователь ранее уже вводил поисковые запросы, например, связанные с мореплаванием под парусом, то при последующем поиске по слову "морские узлы" в приоритетном порядке могут возвращаться сведения об узлах, которые используются в мореплавании.

Во-вторых, Bing может случайным образом выбирать пользователей для опробования новых возможностей, прежде чем делать их общедоступными. Предоставление одного и того же идентификатора клиента при каждом запросе гарантирует, что пользователи, которым был открыт доступ к определенному компоненту, всегда будут иметь к нему доступ. Без идентификатора клиента такой компонент может появляться и исчезать в результатах поиска пользователя как бы случайным образом.

Из-за политик безопасности браузера (CORS) заголовок `X-MSEdge-ClientID` может быть недоступным для кода JavaScript. Это ограничение возникает, когда ответ поиска и страница, его запросившая, имеют разные источники. В рабочей среде такая проблема с политикой решается путем размещения серверного скрипта, который выполняет вызов API, на одном домене с веб-страницей. Так как скрипт будет иметь тот же источник, что и веб-страница, заголовок `X-MSEdge-ClientID` станет доступным для JavaScript.

> [!NOTE]
> В рабочем веб-приложении запрос следует всегда выполнять на стороне сервера. В противном случае вам придется включать в веб-страницу ключ API поиска Bing, где он будет доступен для всех, кто просматривает исходный код. С вас будет взиматься плата за любое использование ресурсов в рамках вашего ключа подписки API, включая запросы, выполненные неавторизованными сторонами, поэтому важно не предоставлять доступ к своему ключу.

В целях разработки запрос к API Bing для поиска в Интернете можно выполнить через прокси-сервер CORS. Ответ от прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит нашему учебному приложению иметь доступ к заголовку идентификатора клиента. Для начала [установите платформу Node.js](https://nodejs.org/en/download/), если она еще не установлена. Затем введите следующую команду в командном окне:

    npm install -g cors-proxy-server

После этого в HTML-файле измените конечную точку службы "Поиск в Интернете Bing" на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Это приведет к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Image Search API v7 reference](tutorial-image-post.md) (Руководство по API для поиска изображений версии 7)

## <a name="see-also"></a>См. также

* [Справочник по API Bing для поиска изображений](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
