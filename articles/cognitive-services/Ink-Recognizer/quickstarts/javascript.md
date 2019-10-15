---
title: Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и Node.js
titleSuffix: Azure Cognitive Services
description: Используйте API Распознавателя рукописного текста, чтобы начать распознавать рукописный текст.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 19626bd68ad82108b2ebaa823d196d0f22008e29
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996914"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и JavaScript

Используйте это краткое руководство, чтобы начать использовать API Распознавателя рукописного текста для рукописных фрагментов. Это приложение JavaScript отправляет запрос API, содержащий данные рукописного фрагмента в формате JSON, и отображает ответ.

Хотя это приложение создается на языке JavaScript и выполняется в вашем веб-браузере, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно вы вызываете API из приложения для рукописного ввода. Этот краткое руководство отправляет данные рукописного фрагмента для следующего рукописного образца из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Предварительные требования

- веб-браузера;
- Пример данных рукописного фрагмента для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Создание ресурса Распознавателя рукописного текста

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. Создайте новый файл `.html` в предпочитаемой интегрированной среде разработки или текстовом редакторе. Затем добавьте в него базовый HTML для кода, который мы создадим позже.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. В теге `<body>` добавьте следующий HTML:
    1. Две области текста для отображения запроса и ответа JSON.
    2. Кнопка для вызова функции `recognizeInk()`, которая будет создана позже.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Загрузка примера данных JSON

1. В теге `<script>` создайте переменную для sampleJson. Затем создайте функцию JavaScript с именем `openFile()`, которая открывает проводник, чтобы вы могли выбрать JSON-файл. При нажатии кнопки `Recognize ink` она вызовет эту функцию и начнет чтение файла.
2. Используйте функцию `onload()` объекта `FileReader` для асинхронной обработки файла. 
    1. Замените пустой строкой любые `\n` или `\r` символы в файле. 
    2. Используйте `JSON.parse()` для преобразования текста в допустимом JSON
    3. Обновите текстовое поле `request` в приложении. Используйте `JSON.stringify()` для форматирования строки JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Отправка запроса в API Распознавателя рукописного текста

1. В теге`<script>` создайте функцию с именем `recognizeInk()`. Позже эта функция вызовет API и обновит страницу с ответом. Добавьте код из следующих действий в этой функции. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Создайте переменные для URL-адреса конечной точки, ключ подписки и пример JSON. Затем создайте объект `XMLHttpRequest`, чтобы отправить запрос API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Создайте функцию возврата для объекта `XMLHttpRequest`. Эта функция будет анализировать ответ API из успешного запроса и отображать его в приложении. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Создайте функцию ошибки для объекта запроса. Эта функция записывает ошибку на консоль. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Создайте функцию для свойства объекта запроса `onreadystatechange`. Когда изменится состояние готовности объекта запроса, будут применяться вышеуказанные функции возврата и ошибки.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Отправьте запрос API. Добавьте ключ подписки в заголовок`Ocp-Apim-Subscription-Key` и установите `content-type` в `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Запуск приложения и просмотр ответа

Это приложение может быть запущено в вашем веб-браузере. Успешный ответ возвращается в формате JSON. Ответ JSON также можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)

Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
