---
title: Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и Java
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
ms.openlocfilehash: e8cd6a4acbd1492bba1c9e88b523a7c44a44f009
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996843"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и Java

В этом кратком руководстве описано, как использовать API Распознавателя рукописного текста для росчерков пера. Это приложение Java отправляет запрос API, содержащий данные росчерка пера в формате JSON, и возвращает ответ.

Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно вы вызываете API из приложения для рукописного ввода. Этот краткое руководство отправляет данные рукописного фрагмента для следующего рукописного образца из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Предварительные требования

- Пакет разработчиков [Java&trade; (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней.

- Импортируйте из репозитория Maven следующие библиотеки:
    - пакет [JSON в Java](https://mvnrepository.com/artifact/org.json/json);
    - пакет [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient).

- Пример данных рукописного фрагмента для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Создание ресурса Распознавателя рукописного текста

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие библиотеки.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Создайте переменные для вашего ключа подписки, конечную точку и файл JSON. Конечная точка затем будет добавлена в конец URI Распознавателя рукописного текста.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `sendRequest()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте объект `CloseableHttpClient`, который может отправлять запросы в API. Отправьте запрос объекту запроса `HttpPut`, объединив конечную точку и URL-адрес Распознавателя рукописного текста.

3. Используйте функцию запроса `setHeader()`, чтобы установить заголовок `Content-Type` в `application/json`, и добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key`.

4. Используйте функцию запроса `setEntity()` для данных, которые необходимо отправить.   

5. Используйте клиентскую функцию `execute()`, чтобы отправить запрос, и сохраните его в объекте `CloseableHttpResponse`. 

6. Создайте объект `HttpEntity`, в котором будет храниться содержимое ответа. Получите содержимое с `getEntity()`. Если ответ не пустой, верните его.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Отправка запроса на распознавание рукописного текста

Создайте метод с именем `recognizeInk()` для распознавания данных росчерка пера. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите созданный метод `sendRequest()`. Получите результат и выведите его в консоль.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Загрузка данных рукописного ввода и отправка запроса

1. В основном методе вашего приложения считайте JSON-файл, содержащий данные, которые будут добавлены к запросам.

2. Вызовите функцию распознавания рукописного ввода, созданную ранее.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Запуск приложения и просмотр ответа

Запустите приложение. Успешный ответ возвращается в формате JSON. Ответ JSON также можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
