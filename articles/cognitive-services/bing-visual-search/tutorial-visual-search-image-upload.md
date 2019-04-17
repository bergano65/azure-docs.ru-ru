---
title: Руководство. Отправка изображения — Визуальный поиск Bing
titleSuffix: Azure Cognitive Services
description: Описание процесса передачи изображения в службу Bing для получения полезных сведений о нем, а также для его анализа и отображения ответа.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 0963c61027358c2c8e971533052631de28994b57
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491089"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Руководство по отправке изображений в API "Визуальный поиск Bing"

API визуального поиска Bing позволяет искать в Интернете изображения, сходные с теми, которые вы отправите в него. С помощью этого руководства вы создадите одностраничное веб-приложение, которое умеет отправлять в API изображение и отображать полученные результаты. Обратите внимание, что это приложение соблюдает не все [требования Bing к использованию и отображению](../bing-web-search/use-display-requirements.md), имеющие отношение к работе с API.

Полный исходный код этого примера с дополнительными возможностями по обработке ошибок и заметками доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

На примере учебного приложения показано, как выполнить такие задачи:

> [!div class="checklist"]
> * Отправка изображения в API визуального поиска Bing
> * Отображение результатов поиска изображения в веб-приложении
> * Изучение аналитических сведений, предоставляемых API

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Создание и структурирование веб-страницы

Создайте страницу HTML, которая отправляет изображение в API "Визуальный поиск Bing", получает аналитические сведения и отображает их. В привычном редакторе или интегрированной среде разработки создайте файл с именем uploaddemo.html. Добавьте в него приведенную ниже базовую структуру HTML.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Разделите страницу на раздел запроса, где пользователь предоставляет сведения для выполнения запроса, и раздел ответа, где отображаются аналитические сведения. Добавьте в `<body>` указанные ниже теги `<div>`. Тег `<hr>` визуально отделяет раздел запроса от раздела ответа.

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Добавьте тег `<script>` в тег `<head>`, чтобы разместить в нем код JavaScript для приложения.

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Получение отправленного файла

Чтобы пользователь мог выбрать изображение для отправки, в нашем приложении используется тег `<input>` с атрибутом типа `file`. В пользовательском интерфейсе должно быть видно, что приложение использует Bing для получения результатов поиска.

Добавьте в `requestSection` `<div>` тег `<div>`. Элемент ввода файлов принимает один файл любого типа изображения (например, JPG, GIF, PNG). Событие `onchange` указывает обработчик, который вызывается, когда пользователь выбирает файл.

Тег `<output>` используется для отображения эскиза выбранного изображения.

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Создание обработчика файла

Создайте функцию обработчика, которая будет считывать отправляемое изображение. Обработчик последовательно перебирает файлы в объекте `FileList` и проверяет, что каждый выбранный файл является файлом изображения с размером не более 1 МБ. Если изображение имеет больший размер, его необходимо уменьшить перед отправкой. И наконец, обработчик отображает эскиз изображения.

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Добавление и сохранение ключа подписки

Ключ подписки требуется приложению для выполнения вызовов к API "Визуальный поиск Bing". В примере для этого руководства вы введете его через пользовательский интерфейс. Добавьте приведенный ниже тег `<input>` (с атрибутом типа text) в раздел `<body>` сразу за тегом `<output>`.

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Используя изображение и ключ подписки, вы можете направить вызов в Визуальный поиск Bing, чтобы получить сведения об этом изображении. В нашем примере в вызове используется рынок по умолчанию (`en-us`) и параметр безопасного поиска (`moderate`).

Приложение позволяет изменять эти значения. Добавьте следующий тег `<div>` под ключом подписки `<div>`. Пример приложения использует тег `<select>` для создания раскрывающегося списка, в котором можно выбрать значения рынка или безопасного поиска. Оба списка отображают выбранное по умолчанию значение.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Добавление параметров поиска на веб-страницу

Приложение позволяет скрыть эти списки в свертываемом элементе `<div>`, которым можно управлять с помощью ссылки Query options (Параметры запроса). Если щелкнуть ссылку "Query options" (Параметры запроса), элемент `<div>` развернется и вы можете просмотреть и изменить параметры запроса. Если еще раз щелкнуть ссылку "Query options" (Параметры запроса), элемент `<div>` свернется, скрыв параметры. В фрагменте ниже показан обработчик `onclick` для ссылки "Query options" (Параметры запроса). Этот обработчик управляет сворачиванием и разворачиванием элемента `<div>`. Добавьте этот обработчик в раздел `<script>`. Обработчик использует все свертываемые элементы `<div>` в демоверсии.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Вызов обработчика `onclick`

Добавьте описанную ниже кнопку `"Get insights"` в раздел body под элементом `<div>` для параметров. Эта кнопка позволяет создать вызов. После нажатия этой кнопки курсор изменяется на вращающийся курсор ожидания и вызывается обработчик `onclick`.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Добавьте обработчик `onclick` `handleQuery()` для этой кнопки в тег `<script>`.

## <a name="handle-the-query"></a>Обработка запроса

Обработчик `handleQuery()` поверяет наличие ключа подписки, его длину (32 символа) и наличие выбранного изображения. Он также очищает полезные сведения из предыдущего запроса. После этого он вызывает функцию `sendRequest()` для совершения вызова.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Отправка поискового запроса

Функция `sendRequest()` форматирует URL-адрес конечной точки, присваивает заголовку `Ocp-Apim-Subscription-Key` значение ключа подписки, добавляет двоичный файл изображения для передачи, настраивает обработчик ответа и выполняет вызов.

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Получение и обработка ответа API

Функция `handleResponse()` обрабатывает ответ на запрос к API визуального поиска Bing. Если вызов завершен успешно, она анализирует ответ JSON, выделяя в нем отдельные теги, которые содержат полезные сведения. Затем он добавляет на страницу результаты поиска. Приложение создает свертываемый элемент `<div>` для каждого тега, что позволяет управлять объемом отображаемых данных. Добавьте этот обработчик в раздел `<script>`.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Синтаксический анализ ответа

Функция `parseResponse` преобразует ответ из формата JSON в объект словаря, последовательно перебирая `json.tags`.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Создание раздела тегов

Функция `buildTagSections()` итеративно обрабатывает обнаруженные теги JSON и вызывает функцию `buildDiv()`, которая создает элемент `<div>` для каждого тега. Каждый тег отображается в виде ссылки. Щелчок по этой ссылке разворачивает тег и отображает связанные с ним сведения. Повторный щелчок по ссылке сворачивает раздел тега.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Отображение результатов поиска на веб-странице

Функция `buildDiv()` вызывает функцию `addDivContent` для создания содержимого свертываемого элемента `<div>` для каждого тега.

Содержимое тега включает в себя код JSON из ответа. Первоначально отображаются только первые 100 символов JSON, но можно щелкнуть строку JSON, чтобы отобразить весь код. Если щелкнуть ее еще раз, строка JSON свернется до 100 знаков.

Добавьте типы действий, найденные в теге. Для каждого типа действия следует вызывать соответствующие функции, чтобы добавить полезные сведения.

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Отображение сведений для разных действий

Следующие функции отображают сведения для разных действий. Эти функции создают изображение или текст с активной ссылкой, которая ведет на веб-страницу с дополнительными сведениями об изображении. Эта страница размещается на сайте Bing.com или на веб-сайте с исходным изображением. В приложении отображаются не все данные. Чтобы просмотреть все доступные поля аналитических сведений, ознакомьтесь со [справочником по Визуальному поиску изображений Bing](https://aka.ms/bingvisualsearchreferencedoc).

> [!NOTE]
> Существуют требования к минимальному объему сведений, отображаемых на странице результатов. Дополнительные сведения см. в статье о [требованиях к использованию и отображению API "Визуальный поиск Bing"](../bing-web-search/use-display-requirements.md).

### <a name="relatedimages-insights"></a>Сведения RelatedImages

Функция `addRelatedImages()` создает заголовок для каждого из веб-сайтов, на которых размещены сходные изображения, последовательно перебирая список действий `RelatedImages` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Сведения PagesIncluding

Функция `addPagesIncluding()` создает ссылку для каждого из веб-сайтов, на которых размещены переданные изображения, последовательно перебирая список действий `PagesIncluding` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>Сведения RelatedSearches

Функция `addRelatedSearches()` создает ссылку для веб-сайта, на котором размещено изображение, последовательно перебирая список действий `RelatedSearches` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Сведения Recipes

Функция `addRecipes()` создает ссылку для каждого возвращаемого рецепта, последовательно перебирая список действий `Recipes` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Сведения Shopping

Функция `addShopping()` создает ссылку для каждого возвращаемого товара, последовательно перебирая список действий `RelatedImages` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Сведения Products

Функция `addProducts()` создает ссылку для каждого возвращаемого товара, последовательно перебирая список действий `Products` и добавляя тег `<img>` в каждый внешний элемент `<div>`.

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>Сведения TextResult

Функция `addTextResult()` отображает любой текст, который был распознан на изображении.

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Функция `addEntity()` отображает ссылку, которая направляет пользователя на сайте Bing.com, где представлены подробные сведения о типе обнаруженной на изображении сущности.

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Функция `addImageWithWebSearchUrl()` отображает в элементе `<div>` изображение с активной ссылкой, которая направляет пользователя на результаты поиска на сайте Bing.com.

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Добавление стиля CSS

Добавьте следующий раздел `<style>` в тег `<head>`, чтобы упорядочить разметку веб-страницы.

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Дополнительная информация

>[!div class="nextstepaction"]
> [Руководство по Поиск похожих изображений из предыдущих операций поиска с помощью токена ImageInsightsToken](./tutorial-visual-search-insights-token.md)
