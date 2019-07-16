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
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: b1c739b6355d3b32063e5289720ed1d191611e65
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721229"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Краткое руководство. Распознавание рукописного ввода с помощью REST API Распознавателя рукописного текста и Java

В этом кратком руководстве описано, как использовать API Распознавателя рукописного текста для росчерков пера. Это приложение Java отправляет запрос API, содержащий данные росчерка пера в формате JSON, и возвращает ответ.

Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно вы вызываете API из приложения для рукописного ввода. Этот краткое руководство отправляет данные росчерка пера для следующего рукописного образца из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Предварительные требования

- Пакет разработчиков [Java&trade; (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней.

- Импортируйте из репозитория Maven следующие библиотеки:
    - пакет [JSON в Java](https://mvnrepository.com/artifact/org.json/json);
    - пакет [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient).

- Пример данных росчерка пера для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. Создайте проект Java в любой интегрированной среде разработки или редакторе, а затем импортируйте в него следующие библиотеки.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Создайте переменные для ключа подписки и конечной точки. Далее приведен универсальный код ресурса (URI), который можно использовать для распознавания рукописного ввода. Для создания URL-адреса запроса API он будет добавлен в конечную точку службы позднее.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `sendRequest()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте объект `CloseableHttpClient`, который может отправлять запросы в API. Отправьте запрос объекту запроса `HttpPut`, объединив конечную точку и URL-адрес Распознавателя рукописного текста.

3. Используйте функцию запроса `setHeader()`, чтобы установить заголовок `Content-Type` в `application/json`, и добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key`.

4. Используйте функцию запроса `setEntity()` для данных, которые необходимо отправить.   

5. Используйте клиентскую функцию `execute()`, чтобы отправить запрос, и сохраните его в объекте `CloseableHttpResponse`. 

6. Создайте объект `HttpEntity`, в котором будет храниться содержимое ответа. Получите содержимое с `getEntity()`. Если ответ не пустой, верните его.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Отправка запроса на распознавание рукописного текста

Создайте метод с именем `recognizeInk()` для распознавания данных росчерка пера. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите созданный метод `sendRequest()`. Получите результат и выведите его в консоль.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Загрузка данных рукописного ввода и отправка запроса

1. В основном методе вашего приложения считайте JSON-файл, содержащий данные, которые будут добавлены к запросам.

2. Вызовите функцию распознавания рукописного ввода, созданную ранее.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

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
