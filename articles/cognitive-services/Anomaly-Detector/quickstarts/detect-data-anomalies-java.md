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
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 6b79470194c5e8dc9b8d51f8b528cb4e51f7daf2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483023"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Java

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение Java отправляет два запроса API, которые содержат данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномалий в пакетном режиме                        | Ответ в формате JSON содержит информацию о состоянии аномалий (и другие данные) для каждой точки в данных временного ряда, а также положения всех обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.                                                                                                                                         |

 Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Предварительные требования

- Пакет разработчиков [Java&trade; (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней.
- Конечная точка и ключ Детектора аномалий
- Импортируйте из репозитория Maven следующие библиотеки:
    - пакет [JSON в Java](https://mvnrepository.com/artifact/org.json/json);
    - пакет [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient).

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. Создайте проект Java и импортируйте следующие библиотеки.
    
    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-java[Initial key and endpoint variables](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Создание функции для отправки запросов

1. Создайте функцию с именем `sendRequest()`, которая принимает ранее созданные переменные. Затем выполните следующие действия.

2. Создайте объект `CloseableHttpClient`, который может отправлять запросы в API. Отправьте запрос объекту запроса `HttpPost`, объединив конечную точку и URL-адрес детектора аномалий.

3. Используйте функцию запроса `setHeader()`, чтобы установить заголовок `Content-Type` в `application/json`, и добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key`.

4. Используйте функцию запроса `setEntity()` для данных, которые необходимо отправить.

5. Используйте клиентскую функцию `execute()`, чтобы отправить запрос, и сохраните его в объекте `CloseableHttpResponse`.

6. Создайте объект `HttpEntity`, в котором будет храниться содержимое ответа. Получите содержимое с `getEntity()`. Если ответ не пустой, верните его.

    [!code-java[API request method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Обнаружение аномалий в пакетном режиме

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detectAnomaliesBatch()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `sendRequest()`, который был создан ранее. Получите результат и выведите его в консоль.

2. Если ответ содержит поле `code`, выведите код ошибки и сообщение об ошибке.

3. В противном случае найдите позиции аномалий в наборе данных. Поле ответа `isAnomaly` содержит логическое значение, которое зависит от того, является ли заданная точка данных аномалией. Получите массив JSON и выполните итерацию по нему, напечатав индекс любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

Создайте метод с именем `detectAnomaliesLatest()`, чтобы обнаружить состояние аномалии последней точки данных в наборе данных. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите метод `sendRequest()`, который был создан ранее. Получите результат и выведите его в консоль.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузка данных временных рядов и отправка запроса

1. В основном методе вашего приложения считайте JSON-файл, содержащий данные, которые будут добавлены к запросам.

2. Вызовите две функции обнаружения аномалий, созданные выше.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Перейдите по ссылками ниже, чтобы просмотреть ответ JSON на GitHub:
* [Пример ответа при пакетном обнаружении](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа при обнаружении последней точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
