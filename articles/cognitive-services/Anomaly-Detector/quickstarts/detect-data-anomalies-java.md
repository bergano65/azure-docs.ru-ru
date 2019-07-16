---
title: Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Java
titleSuffix: Azure Cognitive Services
description: API Детектора аномалий используется для обнаружения отклонений в ряде данных как в пакетном режиме, так и при потоковой передаче.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 2a219dfac597208a2c409f76c035a1b913864245
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721508"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Java

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение Java отправляет два запроса API, которые содержат данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномалий в пакетном режиме                        | В ответе JSON содержится состояние аномалии (и другие данные) для каждой точки данных в данных временных рядов и расположение любых обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

- Пакет разработчиков [Java&trade; (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней.

- Импортируйте из репозитория Maven следующие библиотеки:
    - пакет [JSON в Java](https://mvnrepository.com/artifact/org.json/json);
    - пакет [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient).

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

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

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Методы обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

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
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `sendRequest()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте объект `CloseableHttpClient`, который может отправлять запросы в API. Отправьте запрос объекту запроса `HttpPost`, объединив конечную точку и URL-адрес детектора аномалий.

3. Используйте функцию запроса `setHeader()`, чтобы установить заголовок `Content-Type` в `application/json`, и добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key`.

4. Используйте функцию запроса `setEntity()` для данных, которые необходимо отправить.

5. Используйте клиентскую функцию `execute()`, чтобы отправить запрос, и сохраните его в объекте `CloseableHttpResponse`.

6. Создайте объект `HttpEntity`, в котором будет храниться содержимое ответа. Получите содержимое с `getEntity()`. Если ответ не пустой, верните его.

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

## <a name="detect-anomalies-as-a-batch"></a>Обнаружение аномалий в пакетном режиме

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detectAnomaliesBatch()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `sendRequest()`, который был создан ранее. Получите результат и выведите его в консоль.

2. Если ответ содержит поле `code`, выведите код ошибки и сообщение об ошибке.

3. В противном случае найдите позиции аномалий в наборе данных. Поле ответа `isAnomaly` содержит логическое значение, которое зависит от того, является ли заданная точка данных аномалией. Получите массив JSON и выполните итерацию по нему, напечатав индекс любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

* Создайте метод с именем `detectAnomaliesLatest()`, чтобы обнаружить состояние аномалии последней точки данных в наборе данных. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `sendRequest()`, который был создан ранее. Получите результат и выведите его в консоль.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузка данных временных рядов и отправка запроса

1. В основном методе вашего приложения считайте JSON-файл, содержащий данные, которые будут добавлены к запросам.

2. Вызовите две функции обнаружения аномалий, созданные выше.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Чтобы просмотреть ответы JSON на GitHub, перейдите по следующим ссылкам:
* [Пример ответа обнаружения пакета](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа последнего обнаружения точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
