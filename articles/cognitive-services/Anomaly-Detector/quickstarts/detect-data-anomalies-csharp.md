---
title: Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и C#
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
ms.openlocfilehash: 1b3ed38e7ce8738a0e92775915e894c6e0bbd52a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721560"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и C# 

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение C# отправляет два запроса API, содержащие данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномалий в пакетном режиме                        | Ответ в формате JSON содержит информацию о состоянии аномалий (и другие данные) для каждой точки в данных временного ряда, а также положения всех обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создается на языке C#, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

- Любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/downloads/),

- Платформа [Json.NET](https://www.newtonsoft.com/json), доступная в виде пакета NuGet. Чтобы установить Newtonsoft.Json в качестве пакета NuGet в Visual Studio, сделайте следующее.
    
    1. Щелкните правой кнопкой мыши имя проекта в **обозревателе решений**.
    2. Выберите **Manage NuGet Packages...** (Управление пакетами NuGet...).
    3. Найдите пакет *Newtonsoft.Json* и установите его.

- Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. В Visual Studio создайте новое консольное решение и добавьте следующие пакеты. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
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

1. Создайте новую асинхронную функцию с именем `Request`, принимающую переменные, созданные выше.

2. Установите протокол безопасности клиента и информацию заголовка с помощью объекта `HttpClient`. Не забудьте добавить ключ подписки в заголовок `Ocp-Apim-Subscription-Key`. Затем создайте объект `StringContent` для запроса.

3. Отправьте запрос с `PostAsync()`, а затем верните ответ.

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

## <a name="detect-anomalies-as-a-batch"></a>Обнаружение аномалий в пакетном режиме

1. Создайте новую функцию с именем `detectAnomaliesBatch()`. Создайте запрос и отправьте его, вызвав функцию `Request()` с указанием вашей конечной точки, ключа подписки, URL-адреса для пакетного обнаружения аномалий и данных временного ряда.

2. Десериализируйте объект JSON и выведите его в консоль.

3. Если ответ содержит поле `code`, выведите код ошибки и сообщение об ошибке. 

4. В противном случае найдите положения аномалий в наборе данных. Поле `isAnomaly` ответа содержит массив логических значений, каждое из которых указывает, является ли точка данных аномалией. Преобразуйте его в строковый массив с помощью функции `ToObject<bool[]>()` объекта ответа. Пройдите по массиву, а затем выведите перечень всех `true` значений. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

1. Создайте новую функцию с именем `detectAnomaliesLatest()`. Создайте запрос и отправьте его, вызвав функцию `Request()` с указанием конечной точки, ключа подписки, URL-адреса для обнаружения последней точки аномалии и данных временного ряда.

2. Десериализируйте объект JSON и выведите его в консоль.

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

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузка данных временных рядов и отправка запроса

1. В методе main приложения загрузите данные временного ряда в формате JSON с помощью `File.ReadAllText()`. 

2. Вызовите функции обнаружения аномалий, созданные ранее. Чтобы после выполнения приложения оставить консоль открытой, используйте `System.Console.ReadKey()`.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Перейдите по ссылками ниже, чтобы просмотреть ответ JSON на GitHub:
* [Пример ответа при пакетном обнаружении](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа при обнаружении последней точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
