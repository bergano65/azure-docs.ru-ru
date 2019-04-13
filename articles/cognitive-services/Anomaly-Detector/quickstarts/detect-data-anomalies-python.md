---
title: Краткое руководство. Обнаруживать аномалии в пакетном режиме, с помощью REST API обнаружения аномалий и Python | Документация Майкрософт
description: Используйте API обнаружения аномалий для обнаружения отклонений в ряду данных в пакетном режиме или при потоковой передаче данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544506"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Краткое руководство. Обнаруживать аномалии в данных временных рядов с помощью REST API обнаружения аномалий и Python

Используйте в этом кратком руководстве, чтобы начать использовать API обнаружения аномалий два режима обнаружения для обнаружения аномалий в данных временных рядов. Это приложение Python отправляет два API запросов, содержащих данные ряда времени в формате JSON и возвращает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаруживать аномалии в пакетном режиме                        | Ответ JSON, содержащий состояние аномалий (и другие данные) для каждой точки данных в данных временных рядов и положения любые обнаруженные аномалии. |
| Обнаружение аномалий состояние последней точки данных | Ответ JSON, содержащий состояние аномалий (и другие данные), наличие последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования.

## <a name="prerequisites"></a>Технические условия

- [Python версии 2.x или 3.x](https://www.python.org/downloads/)

- [Библиотеку запросов](http://docs.python-requests.org) для python

- Указывает JSON файла содержащего данных временных рядов. Данные для примера в этом кратком руководстве можно найти на [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Создание приложения

1. В предпочитаемом текстовом редакторе или интегрированной среды разработки создайте новый файл python. Добавьте следующие импорты.

    ```python
    import requests
    import json
    ```

2. Создайте переменные для ключ подписки и конечной точки. Ниже приведены идентификаторы URI, можно использовать для обнаружения аномалий. Они будут добавлены к конечной точке службы позднее для создания API URL-адреса запросов.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Обнаружение пакета    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение на последние точки данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Чтение в JSON-файле данных, открыв его и с помощью `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте новую функцию `send_request()` , которая принимает переменные, созданные выше. Затем выполните следующие действия.

2. Создание словаря для заголовков запросов. Задайте `Content-Type` для `application/json`и добавьте ключ подписки для `Ocp-Apim-Subscription-Key` заголовка.

3. Отправка запроса с использованием `requests.post()`. Объединение конечной точки и URL-адреса обнаружения аномалий полный URL-адрес запроса, а также включают заголовки и данные запроса json. И затем возвращают ответ.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Обнаруживать аномалии в пакетном режиме

1. Создайте метод с именем `detect_batch()` для обнаружения аномалий в данных в пакетном режиме. Вызовите `send_request()` метод, созданный ранее с помощью конечной точки, URL-адрес, ключ подписки и данных json.

2. Вызовите `json.dumps()` результат для его форматирования и вывода его на консоль.

3. Если в ответе содержится `code` поле, печати, код ошибки и сообщение об ошибке.

4. В противном случае поиск позиции аномалии в наборе данных. Ответ `isAnomaly` поле содержит значение типа boolean, относящиеся к ли точки данных — аномалия. Итерацию по списку и печать индекса любого `True` значения. Эти значения соответствуют индекс точки данных подозрительной активности, если любой объект найден.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение аномалий состояние последней точки данных

1. Создайте метод с именем `detect_latest()` для определения того, является ли последние точки данных ряда времени аномалий. Вызовите `send_request()` метод выше с помощью конечной точки, URL-адрес, ключ подписки и данных json. 

2. Вызовите `json.dumps()` результат для его форматирования и вывода его на консоль.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузить данные временных рядов и отправить запрос

1. Загрузить данные JSON временных рядов открыв обработчика файлов и с помощью `json.load()` на нем. Затем следует вызовите созданную выше методов обнаружения аномалий.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Щелкните ссылки ниже, чтобы просмотреть ответ JSON на сайте GitHub:
* [Пример ответа на обнаружение](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример последнюю точку обнаружения ответа](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)