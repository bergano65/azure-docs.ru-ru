---
title: Краткое руководство. Обнаружение аномалий в пакетном режиме с помощью REST API "Детектор аномалий" и Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: API Детектора аномалий используется для обнаружения отклонений в ряде данных как в пакетном режиме, так и при потоковой передаче.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: e24436cef11cc07571adb55bca63add5bda783c4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483399"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Python

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение Python отправляет два запроса API, которые содержат данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномалий в пакетном режиме                        | Ответ в формате JSON содержит информацию о состоянии аномалий (и другие данные) для каждой точки в данных временного ряда, а также положения всех обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создается на языке Python, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Предварительные требования

- [Python версии 2.x или 3.x](https://www.python.org/downloads/)
- Конечная точка и ключ Детектора аномалий
- [Библиотека запросов](https://pypi.org/project/requests/) для Python

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Создание приложения

1. Создайте файл Python и добавьте следующие импорты.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Чтобы прочитать файл данных JSON, сначала его следует открыть, а затем использовать `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `send_request()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте словарь для заголовков запросов. Задайте `Content-Type` для `application/json` и добавьте ключ подписки в заголовок `Ocp-Apim-Subscription-Key`.

3. Отправьте запрос с помощью `requests.post()`. Объедините конечную точку и URL-адрес обнаружения аномалии для полного запроса URL-адреса (включая заголовки и данные запроса в формате JSON). А затем получите ответ.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Обнаружение аномальных действий в пакетном режиме

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detect_batch()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `send_request()`, который был создан ранее.

2. Чтобы отформатировать `json.dumps()`, вызовите его в результате и выведите его в консоль.

3. Если ответ содержит поле `code`, выведите код ошибки и сообщение ошибки.

4. В противном случае найдите позиции аномалий в наборе данных. Поле ответа `isAnomaly` содержит логическое значение, которое зависит от того, является ли заданная точка данных аномалией. Пройдите по списку, а затем выведите перечень любых `True` значений. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

1. Чтобы определить, является ли последняя точка данных аномалией, создайте метод `detect_latest()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `send_request()`. 

2. Чтобы отформатировать `json.dumps()`, вызовите его в результате и выведите его в консоль.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Отправка запроса

Вызовите методы обнаружения аномалий, созданные ранее.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Перейдите по ссылками ниже, чтобы просмотреть ответ JSON на GitHub:
* [Пример ответа при пакетном обнаружении](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа при обнаружении последней точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
