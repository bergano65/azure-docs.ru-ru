---
title: Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода и Java
description: Используйте API распознавания рукописного ввода для запуска, распознавая цифровых рукописных штрихов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6237253922544dc47bb11aec4dd58139f99eb0da
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518617"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Краткое руководство. Распознавать рукописный ввод с помощью REST API распознавания рукописного ввода и Java

Используйте в этом кратком руководстве, чтобы начать работу с API распознавания рукописного ввода на цифровых рукописных штрихов. Это приложение Java отправляет запрос API, содержащий данные рукописного ввода на формате JSON и возвращает ответ.

Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

Обычно будет вызов API из цифрового рукописного ввода приложения. В этом кратком руководстве отправляет данные штрихов рукописного ввода для следующего примера рукописных из JSON-файла.

![изображение рукописного текста](../media/handwriting-sample.jpg)

Исходный код для этого краткого руководства можно найти на портале [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Технические условия

- [Java&trade; 7 Kit(JDK) разработки](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней версии.

- Импортируйте эти библиотеки из репозитория Maven
    - [JSON в Java](https://mvnrepository.com/artifact/org.json/json) пакета
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) пакета

- Данные stroke рукописного ввода для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

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

2. Создайте переменные для ключ подписки и конечной точки. Ниже приведен URI, который можно использовать для распознавания рукописного ввода. Он будет добавляться к конечной точке службы позднее для создания URL-АДРЕСЕ запроса API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую функцию `sendRequest()` , которая принимает переменные, созданные выше. Затем выполните следующие действия.

2. Создание `CloseableHttpClient` объект, который может отправлять запросы к API. Отправка запроса на `HttpPut` объект запроса, объединяя конечной точки и распознавания рукописного ввода URL-адрес.

3. Используйте запрос `setHeader()` функцию для задания `Content-Type` заголовок `application/json`и добавьте ключ подписки для `Ocp-Apim-Subscription-Key` заголовка.

4. Используйте запрос `setEntity()` функцию для отправки данных.   

5. Использование клиента `execute()` функцию для отправки запроса и сохраните его для `CloseableHttpResponse` объекта. 

6. Создание `HttpEntity` объект для сохранения содержимого ответа. Получить содержимое с `getEntity()`. Если ответ не является пустым, вернуть его.
    
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

## <a name="send-an-ink-recognition-request"></a>Отправить запрос распознавания рукописного ввода

Создайте метод с именем `recognizeInk()` для распознавания рукописного ввода данных штриха. Вызовите `sendRequest()` метод, созданный ранее с помощью конечной точки, URL-адрес, ключ подписки и данных json. Получить результат и его вывода на консоль.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Загрузить данные рукописного ввода и отправки запроса

1. В методе main приложения чтения в JSON-файл, содержащий данные, которые будут добавлены на запросы.

2. Вызов функции распознавания рукописного ввода, созданной ранее.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Запустите приложение и просмотреть ответ

Запустите приложение. Успешный ответ возвращается в формате JSON. Можно также найти ответ JSON на [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://go.microsoft.com/fwlink/?linkid=2089907)


Чтобы увидеть, как API Распознавателя рукописного текста работает в приложении для рукописного ввода, взгляните на следующие примеры приложений на GitHub.
* [C# и универсальная платформа Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# и Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Приложение браузера Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Мобильное приложение для Java и Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Мобильное приложение для Swift и iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
