---
title: Краткое руководство. Обнаружение аномалий в пакетном режиме с помощью REST API "Детектор аномалий" и Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Использование API Детектора аномалий для обнаружения аномалий для рядов данных в виде пакета или потоковой передача данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: c69bc4db35a198d73f9b29ee3ed2fa6b6f71be49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721447"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Python

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение Python отправляет два запроса API, которые содержат данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномальных действий в пакетном режиме                        | В ответе JSON содержится состояние аномалии (и другие данные) для каждой точки данных в данных временных рядов и расположение любых обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Предварительные требования

- [Python версии 2.x или 3.x](https://www.python.org/downloads/)

- [Библиотека запросов](http://docs.python-requests.org) для Python

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Создание приложения

1. Создайте файл Python в любом удобном текстовом редакторе или интегрированной среде разработки. Добавьте приведенные ниже импортированные данные.

    ```python
    import requests
    import json
    ```

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Методы обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Чтобы прочитать файл данных JSON, сначала его следует открыть, а затем использовать `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `send_request()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте словарь для заголовков запросов. Задайте `Content-Type` для `application/json` и добавьте ключ подписки в заголовок `Ocp-Apim-Subscription-Key`.

3. Отправьте запрос с помощью `requests.post()`. Объедините конечную точку и URL-адрес обнаружения аномалии для полного запроса URL-адреса (включая заголовки и данные запроса в формате JSON). А затем получите ответ.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Обнаружение аномальных действий в пакетном режиме

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detect_batch()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `send_request()`, который был создан ранее.

2. Чтобы отформатировать `json.dumps()`, вызовите его в результате и выведите его в консоль.

3. Если ответ содержит поле `code`, выведите код ошибки и сообщение ошибки.

4. В противном случае найдите позиции аномалий в наборе данных. Поле ответа `isAnomaly` содержит логическое значение, которое зависит от того, является ли заданная точка данных аномалией. Пройдите по списку, а затем выведите перечень любых `True` значений. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

1. Чтобы определить, является ли последняя точка данных аномалией, создайте метод `detect_latest()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `send_request()`. 

2. Чтобы отформатировать `json.dumps()`, вызовите его в результате и выведите его в консоль.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузка данных временных рядов и отправка запроса

1. Чтобы загрузить данные временных рядов в формате JSON, откройте обработчик файлов и используйте `json.load()`. Затем вызовете методы обнаружения аномалий, созданные ранее.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Чтобы просмотреть ответы JSON на GitHub, перейдите по следующим ссылкам.
* [Пример ответа обнаружения пакета](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа последнего обнаружения точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
