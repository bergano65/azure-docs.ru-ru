---
title: 'Руководство: создание одностраничного веб-приложения — Визуальный поиск Bing'
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как использовать API визуального поиска Bing в одностраничном веб-приложении.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: fe7159e88bd70ba8af23909559264fa5f210ef10
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443908"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Руководство по одностраничным веб-приложениям для наглядного поиска

API Bing для наглядного поиска предоставляет примерно такие же возможности, как просмотр сведений об изображении на Bing.com/images. Функция визуального поиска позволяет указать изображение и получить о нем аналитические сведения, например визуально подобные изображения, ресурсы покупок, веб-страницы, которые содержат это изображения, и многое другое. 

Для выполнения действий, описанных в этом руководстве, нужно создать подписку в ценовой категории S9, как указано на странице [Цены на Cognitive Services. API-интерфейсы поиска Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Чтобы создать подписку на портале Azure:
1. Введите запрос BingSearchV7 в строке поиска с текстом `Search resources, services, and docs` в верхней части страницы на портале Azure.  
2. В меню Marketplace в раскрывающемся списке выберите `Bing Search v7`.
3. В поле `Name` (Имя) введите имя нового ресурса.
4. Выберите подписку `Pay-As-You-Go` (Оплата по мере использования).
5. Выберите ценовую категорию `S9`.
6. Щелкните `Enable` (Активировать), чтобы создать подписку.

Это руководство дополняет сведения по одностраничному веб-приложению, которые приведены в руководстве по службе "Поиск изображений Bing" (см. [Руководство: одностраничное веб-приложение](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Полный исходный код для работы с этим руководством приведен в разделе [Одностраничное веб-приложение (исходный код)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Итоговый исходный код этого руководства приведен в разделе [Одностраничное веб-приложение для наглядного поиска](tutorial-bing-visual-search-single-page-app-source.md).

Рассматриваются такие задачи:

> [!div class="checklist"]
> * Вызов API Bing для наглядного поиска с помощью токена аналитики изображения.
> * Отображение похожих изображений.

## <a name="call-bing-visual-search"></a>Вызов API Bing для наглядного поиска
Измените код, приведенный в руководстве по службе "Поиск изображений Bing". Для этого добавьте следующий код в конец элемента сценария в строке 409. Этот код вызывает API Bing для наглядного поиска и отображает результаты.

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}

function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Запись токена аналитики
Добавьте следующий код в объект `searchItemsRenderer` в строке 151. Этот код добавляет ссылку **find similar** (найти похожие), которая при нажатии вызывает функцию `bingVisualSearch`. Функция получает токен imageInsightsToken в качестве аргумента.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Отображение похожих изображений
Добавьте следующий HTML-код в строку 601. Этот код разметки добавляет элемент, используемый для отображения результатов вызова API Bing для наглядного поиска.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Когда все новые фрагменты кода JavaScript и HTML-элементы на своих местах, результаты поиска отображаются при нажатии ссылки **find similar**. Щелкните эту ссылку, чтобы раздел **Similar** (Похожие) заполнился изображениями, похожими на выбранное вами изображение. Для просмотра изображений может потребоваться развернуть раздел **Similar**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Одностраничное веб-приложение для наглядного поиска (исходный код)](tutorial-bing-visual-search-single-page-app-source.md)
> [Справочник по API Bing для наглядного поиска](https://aka.ms/bingvisualsearchreferencedoc)