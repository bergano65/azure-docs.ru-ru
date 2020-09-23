---
title: Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Java
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать API "Детектор аномалий" для обнаружения отклонений в ряде данных как в пакетном режиме, так и при потоковой передаче.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 6c37ac4a8e43f8e11e37186e2438c4803556339e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905767"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием REST API Детектора аномалий и Java

Используйте это краткое руководство, чтобы узнать, как начать использовать две модели API Детектора аномалий для обнаружения аномалий в данных временных рядов. Это приложение Java отправляет запросы API, которые содержат данные временных рядов в формате JSON, и получает ответы.

| Запрос API                                        | Выходные данные приложения                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Обнаружение аномалий в пакетном режиме                        | Ответ в формате JSON содержит информацию о состоянии аномалий (и другие данные) для каждой точки в данных временного ряда, а также положения всех обнаруженных аномалий. |
| Обнаружение состояний аномалии последней точки данных | В ответе JSON содержится состояние аномалии (и другие данные) для последней точки данных в данных временных рядов.   |
| Обнаружение точек изменения, отмечающих новые тенденции в данных | Ответ JSON, содержащий обнаруженные точки изменения в данных временных рядов. |

Хотя это приложение создано на языке Java, API представляет собой веб-службу RESTful, совместимую с большинством языков программирования. Исходный код для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/java-detect-anomalies.java).

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
- Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Создание ресурса Детектора аномалий"  target="_blank">созданию ресурса Детектора аномалий <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    - Для подключения приложения к API "Детектор аномалий" потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
- Пакет разработчиков [Java&trade; (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) или более поздней.
- Импортируйте из репозитория Maven следующие библиотеки:
    - пакет [JSON в Java](https://mvnrepository.com/artifact/org.json/json);
    - пакет [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient).

- JSON-файл содержит точки данных временного ряда. Пример данных для этого краткого руководства можно найти на портале [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Создание приложения

1. Создайте проект Java и импортируйте следующие библиотеки.

    [!code-java[Import statements](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=imports)]

2. Создайте переменные для ключа подписки и конечной точки. Далее приведены универсальные коды ресурса (URI), которые можно использовать для обнаружения аномалий. Для создания URL-адресов запроса API они будут добавлены в конечную точку службы позднее.

    |Метод обнаружения  |URI  |
    |---------|---------|
    |Пакетное обнаружение    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Обнаружение в последней точке данных     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Обнаружение точек изменений | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

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

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detectAnomaliesBatch()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите созданный метод `sendRequest()`. Получите результат и выведите его в консоль.

2. Если ответ содержит поле `code`, выведите код ошибки и сообщение об ошибке.

3. В противном случае найдите положения аномалий в наборе данных. Поле ответа `isAnomaly` содержит логическое значение, которое зависит от того, является ли заданная точка данных аномалией. Получите массив JSON и выполните итерацию по нему, напечатав индекс любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

    [!code-java[Method for batch detection](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

Создайте метод с именем `detectAnomaliesLatest()`, чтобы обнаружить состояние аномалии последней точки данных в наборе данных. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите созданный метод `sendRequest()`. Получите результат и выведите его в консоль.

[!code-java[Latest point detection method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectLatest)]


## <a name="detect-change-points-in-the-data"></a>Обнаружение точек изменения в данных

1. Чтобы выполнить пакетное обнаружение данных, создайте метод `detectChangePoints()`. С помощью конечной точки, URL-адреса, ключа подписки и данных JSON вызовите созданный метод `sendRequest()`. Получите результат и выведите его в консоль.

2. Если ответ содержит поле `code`, выведите код ошибки и сообщение об ошибке.

3. В противном случае найдите положения точек изменения в наборе данных. Поле ответа `isChangePoint` содержит логическое значение, которое указывает, является ли заданная точка данных точной изменения тенденции. Получите массив JSON и выполните итерацию по нему, выведя индекс любых значений `true`. Эти значения соответствуют индексам точек изменения тенденций, если они были обнаружены.

    [!code-java[detect change points](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=detectChangePoint)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Загрузка данных временных рядов и отправка запроса

1. В основном методе вашего приложения считайте JSON-файл, содержащий данные, которые будут добавлены к запросам.

2. Вызовите две функции обнаружения аномалий, созданные выше.

    [!code-java[Main method](~/samples-anomaly-detector/quickstarts/java-detect-anomalies.java?name=main)]

### <a name="example-response"></a>Пример ответа

Успешный ответ возвращается в формате JSON. Перейдите по ссылками ниже, чтобы просмотреть ответ JSON на GitHub:
* [Пример ответа при пакетном обнаружении](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Пример ответа при обнаружении последней точки](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Пример ответа при обнаружении точки изменения](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
