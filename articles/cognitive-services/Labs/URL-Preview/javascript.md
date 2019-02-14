---
title: Краткое руководство. Служба предварительного просмотра URL-адресов в проектах, JavaScript
titlesuffix: Azure Cognitive Services
description: Пример скрипта для быстрого начала работы с API предварительного просмотра URL-адресов Bing с использованием JavaScript.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 1f3c37e770c2edd76bd299771648b2de29d42dd9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873654"
---
# <a name="quickstart-url-preview-in-javascript"></a>Краткое руководство. Использование службы предварительного просмотра URL-адресов с JavaScript 

В следующем одностраничном приложении с помощью JavaScript создается представление для предварительного просмотра URL-адреса сайта SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Предварительные требования

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview).

## <a name="code-scenario"></a>Сценарий кода
В следующем примере JavaScript содержится объект для ввода данных в текстовое поле, куда пользователь вводит URL-адрес для предварительного просмотра.  При нажатии пользователем кнопки **Preview** (Просмотр) метод onclick перенаправляется в `getPreview`, где код формирует веб-запрос к конечной точке **UrlPreview**.

Код создает *XMLHttpRequest*, добавляет заголовок *Ocp-Apim-Subscription-Key* и ключ, а затем отправляет запрос.  При этом для обработки ответа добавляется обработчик асинхронных событий.

Если ответ успешно возвращается, обработчик назначает текст JSON ответа абзацу `demo` на странице. Другие элементы ответа обрабатываются в следующих абзацах для отображения.

**Необработанный ответ JSON**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**Запущенная демонстрация**

![Пример предварительного просмотра URL-адреса на JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Запуск приложения

Чтобы запустить приложение, выполните следующие действия:

1. Замените значение `YOUR-SUBSCRIPTION-KEY` действующим ключом доступа для своей подписки.
2. Сохраните HTML и скрипт в файле с расширением .html.
3. Запустите веб-страницу в браузере.
4. Используйте существующий URL-адрес или введите другой в текстовое поле.
5. Нажмите кнопку **Preview** (Просмотр).

**Исходный код:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](csharp.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство для Node](node-quickstart.md)
- [Краткое руководство для Python](python-quickstart.md)
