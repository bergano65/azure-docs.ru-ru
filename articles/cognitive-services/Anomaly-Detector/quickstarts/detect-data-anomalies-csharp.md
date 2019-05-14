---
title: Краткое руководство. Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий и C# | Документация Майкрософт
description: Используйте API обнаружения аномалий для обнаружения отклонений в ряду данных в пакетном режиме или при потоковой передаче данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04b331f3b63ad6400b4bb8efcd053d04ac88989b
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595839"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Краткое руководство. Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий иC# 

Используйте в этом кратком руководстве, чтобы начать использовать API обнаружения аномалий два режима обнаружения для обнаружения аномалий в данных временных рядов. Это C# приложение отправляет два запроса API, содержащий данные формата JSON временных рядов и возвращает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаруживать аномалии в пакетном режиме                        | Ответ JSON, содержащий состояние аномалий (и другие данные) для каждой точки данных в данных временных рядов и положения любые обнаруженные аномалии. |
| Обнаружение аномалий состояние последней точки данных | Ответ JSON, содержащий состояние аномалий (и другие данные), наличие последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Технические условия

- Любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/downloads/),

- Платформа [Json.NET](https://www.newtonsoft.com/json), доступная в виде пакета NuGet. Установка Newtonsoft.Json в виде пакета NuGet в Visual Studio:
    
    1. Щелкните правой кнопкой мыши проект в **обозревателе решений**.
    2. Выберите **управление пакетами NuGet**.
    3. Поиск *Newtonsoft.Json* и установить пакет.

- Если вы используете Linux и Mac OS, это приложение может выполняться с помощью [Mono](https://www.mono-project.com/).

- Указывает JSON файла содержащего данных временных рядов. Данные для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. В Visual Studio создайте новое решение консоли и добавьте следующие пакеты. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Создайте переменные для ключ подписки и конечной точки. Ниже приведены идентификаторы URI, можно использовать для обнаружения аномалий. Они будут добавлены к конечной точке службы позднее для создания API URL-адреса запросов.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Обнаружение пакета    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение на последние точки данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую функцию async `Request` , принимающий переменные, созданные выше.

2. Задать протокол безопасности и сведения заголовка с помощью клиента `HttpClient` объекта. Не забудьте добавить ключ подписки для `Ocp-Apim-Subscription-Key` заголовка. Затем создайте `StringContent` для запроса.

3. Отправка запроса с `PostAsync()`, а затем возвращают ответ.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Обнаруживать аномалии в пакетном режиме

1. Создайте новую функцию `detectAnomaliesBatch()`. Создать запрос и отправить его, вызвав `Request()` функцию с конечной точкой, ключ подписки, URL-адрес для обнаружения аномалий пакетной службы и данные временных рядов.

2. Десериализовать объект JSON и вывести его на консоль.

3. Если в ответе содержится `code` поле, печати, код ошибки и сообщение об ошибке. 

4. В противном случае поиск позиции аномалии в наборе данных. Ответ `isAnomaly` поле содержит массив логических значений, каждое из которых указывает, является ли точка данных аномалии. Преобразовать это значение в массив строк, содержащий объект ответа `ToObject<bool[]>()` функции. Итерации в массиве и печать индекса любого `true` значения. Эти значения соответствуют индекс точки данных подозрительной активности, если любой объект найден.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение аномалий состояние последней точки данных

1. Создайте новую функцию `detectAnomaliesLatest()`. Создать запрос и отправить его, вызвав `Request()` функцию с конечной точкой, ключ подписки, URL-адрес для обнаружения аномалий последнюю точку и данные временных рядов.

2. Десериализовать объект JSON и вывести его на консоль.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузить данные временных рядов и отправить запрос

1. В методе main приложения загрузить данные JSON временных рядов с `File.ReadAllText()`. 

2. Вызов функций обнаружения аномалий, созданной ранее. Используйте `System.Console.ReadKey()` для не закрывайте окно консоли после запуска приложения.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Щелкните ссылки ниже, чтобы просмотреть ответ JSON на сайте GitHub:
* [Пример ответа на обнаружение](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример последнюю точку обнаружения ответа](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
