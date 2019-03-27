---
title: Краткое руководство. Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий и Java | Документация Майкрософт
description: Используйте API обнаружения аномалий для обнаружения отклонений в ряду данных в пакетном режиме или при потоковой передаче данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 2a6d356bcf65cdb59e84b34cf2a7478de796e61a
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473285"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Краткое руководство. Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий и Java

Используйте в этом кратком руководстве, чтобы начать использовать API обнаружения аномалий два режима обнаружения для обнаружения аномалий в данных временных рядов. Это приложение Java отправляет два API запросов, содержащих данные ряда времени в формате JSON и возвращает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаруживать аномалии в пакетном режиме                        | Ответ JSON, содержащий состояние аномалий (и другие данные) для каждой точки данных в данных временных рядов и положения любые обнаруженные аномалии. |
| Обнаружение аномалий состояние последней точки данных | Ответ JSON, содержащий состояние аномалий (и другие данные), наличие последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Технические условия

- [Java&trade; 7 Kit(JDK) разработки](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней версии.

- Импортируйте эти библиотеки из репозитория Maven
    - [JSON в Java](https://mvnrepository.com/artifact/org.json/json) пакета
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) пакета

- Указывает JSON файла содержащего данных временных рядов. Данные для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Создайте переменные для ключ подписки и конечной точки. Ниже приведены идентификаторы URI, можно использовать для обнаружения аномалий. Они будут добавлены к конечной точке службы позднее для создания API URL-адреса запросов.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Обнаружение пакета    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение на последние точки данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Чтение в файле данных JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую функцию `sendRequest()` , которая принимает переменные, созданные выше. Затем выполните следующие действия.

2. Создание `CloseableHttpClient` объект, который может отправлять запросы к API. Отправка запроса на `HttpPost` объект запроса, объединяя конечной точки и URL-адреса обнаружения аномалий.

3. Используйте запрос `setHeader()` функцию для задания `Content-Type` заголовок для `application/json`и добавьте ключ подписки для `Ocp-Apim-Subscription-Key` заголовка.

4. Используйте запрос `setEntity()` функцию для отправки данных.   

5. Использование клиента `execute()` функцию для отправки запроса и сохраните его для `CloseableHttpResponse` объекта. 

6. Создание `HttpEntity` объект для сохранения содержимого ответа. Получить содержимое с `getEntity()`. Если ответ не является пустым, вернуть его.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
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
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Обнаруживать аномалии в пакетном режиме

1. Создайте метод с именем `detectAnomaliesBatch()` для обнаружения аномалий в данных в пакетном режиме. Вызовите `sendRequest()` метод, созданный ранее с помощью конечной точки, URL-адрес, ключ подписки и данных json. Получить результат и его вывода на консоль.

2. Найти позиции аномалии в наборе данных. Ответ `isAnomaly` поле содержит значение типа boolean, относящиеся к ли точки данных — аномалия. Возвращает массив JSON и итерации, печать индекса любого `true` значения. Эти значения соответствуют индекс точки данных подозрительной активности, если любой объект найден.

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение аномалий состояние последней точки данных

* Создайте метод с именем `detectAnomaliesLatest()` для обнаружения аномалий состояние последней точки данных в наборе данных. Вызовите `sendRequest()` метод, созданный ранее с помощью конечной точки, URL-адрес, ключ подписки и данных json. Получить результат и его вывода на консоль.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузить данные временных рядов и отправить запрос

1. В методе main приложения чтения в JSON-файл, содержащий данные, которые будут добавлены на запросы.

2. Вызовите функции обнаружения аномалий два, созданной ранее.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Щелкните ссылки ниже, чтобы просмотреть ответ JSON на сайте GitHub:
* [Пример ответа на обнаружение](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример последнюю точку обнаружения ответа](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)