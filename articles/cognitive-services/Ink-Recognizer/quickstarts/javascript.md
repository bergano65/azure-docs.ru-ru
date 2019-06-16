---
title: Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода и Node.js
description: Используйте API распознавания рукописного ввода для запуска, распознавая цифровых рукописных штрихов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 0dc672f0efc420ab73fd923191c2bd52fb571a4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056854"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода и JavaScript

Используйте в этом кратком руководстве, чтобы начать работу с API распознавания рукописного ввода на цифровых рукописных штрихов. Это приложение JavaScript отправляет запрос API, содержащий данные рукописного ввода на формате JSON и выводит ответ.

Хотя это приложение написано на Javascript и выполняется в веб-браузере, этот API доступен веб-службу RESTful совместим с большинство языков программирования.

Обычно будет вызов API из цифрового рукописного ввода приложения. В этом кратком руководстве отправляет данные штрихов рукописного ввода для следующего примера рукописных из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Технические условия

- веб-браузера;
- Данные stroke рукописного ввода для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. В избранной IDE или редактор, создайте новый `.html` файл. Затем добавьте в него простой HTML-документ для кода, который мы добавим позже.
    
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

2. В рамках `<body>` , добавьте следующий код html:
    1. Две области текста для отображения запроса и ответа JSON.
    2. Кнопки для вызова `recognizeInk()` функции, который будет создан позже.
    
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

## <a name="load-the-example-json-data"></a>Загрузить пример данных JSON

1. В рамках `<script>` тег, создайте переменную для sampleJson. Затем создайте функцию JavaScript с именем `openFile()` , откроется окно проводника, поэтому его можно выбрать JSON-файл. Когда `Recognize ink` кнопки, он будет вызывать эту функцию и начать чтение файла.
2. Используйте `FileReader` объекта `onload()` функции для обработки файла асинхронно. 
    1. Заменить `\n` или `\r` символов в файле с пустой строкой. 
    2. Используйте `JSON.parse()` для преобразования текста в допустимый объект JSON
    3. Обновление `request` текстовое поле в приложении. Используйте `JSON.stringify()` отформатировать строку JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Отправьте запрос в API распознавания рукописного ввода

1. В рамках `<script>` тег, создайте функцию с именем `recognizeInk()`. Позже эта функция будет вызывать API и обновление страницы с ответом. Добавьте в код следующие действия, описанные в этой функции. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Создайте переменные для URL-адрес конечной точки, ключ подписки и пример JSON. Затем создайте `XMLHttpRequest` объект для отправки запроса API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Функция возврата для создания `XMLHttpRequest` объекта. Эта функция будет проанализировать ответ API от успешного запроса и отображения его в приложении. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Создайте функцию ошибки для объекта запроса. Эта функция заносит ошибку в консоль. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Создание функции для запроса объекта `onreadystatechange` свойство. При изменении состояния готовности объект запроса, будет применяться функций, описанных выше возвращаемое значение и ошибки.
            
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
    
    5. Отправка запроса API. Добавьте ключ подписки `Ocp-Apim-Subscription-Key` заголовка, а также набор `content-type` для `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
