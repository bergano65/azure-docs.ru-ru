---
title: Краткое руководство по использованию клиентской библиотеки Детектора аномалий (Python)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 98f68af11cf21cb795e7741585e55c195c066995
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024852"
---
Приступите к работе с клиентской библиотекой Детектора аномалий для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба Детектора аномалий позволяет находить аномалии в данных временных рядов, автоматически применяя для них наиболее подходящие модели, независимо от отрасли, сценария или объема данных.

Клиентскую библиотеку Детектора аномалий для Python можно использовать для таких задач:

* обнаружение аномалий в наборе данных временных рядов с использованием пакетного запроса;
* Обнаружение состояния аномалии последней точки данных во временном ряду.
* обнаружение точек изменения тенденций в наборе данных.

[Справочная документация по библиотеке](https://go.microsoft.com/fwlink/?linkid=2090370) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Пакет (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Поиск примера кода на GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Предварительные требования

* [Python 3.x](https://www.python.org/)
* [Библиотека анализа данных Pandas](https://pandas.pydata.org/).
* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Создание ресурса Детектора аномалий"  target="_blank">созданию ресурса Детектора аномалий <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    * Для подключения приложения к API "Детектор аномалий" потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.


## <a name="setting-up"></a>Настройка

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Создание приложения Python

 Создайте файл Python и импортируйте следующие библиотеки.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Создайте переменные для ключа в качестве переменной среды, путь к файлу данных временных рядов, а также расположение Azure для вашей подписки. Например, `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Объектная модель

Клиент Детектора аномалий — это объект [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python), который выполняет проверку подлинности в Azure с помощью вашего ключа. Клиент может выполнять обнаружение аномалий с использованием [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) для всего набора данных и [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) для последней точки данных. Функция [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) обнаруживает точки, которые отмечают изменения тенденции.

Данные временного ряда отправляются в виде ряда [точек](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) в объекте [запроса](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python). Объект `Request` содержит свойства для описания данных (например, [степень детализации](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)) и параметры для обнаружения аномалий.

Ответ Детектора аномалий — это объект [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) или [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) (в зависимости от используемого метода).

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Детектора аномалий для Python:

* [аутентификация клиента](#authenticate-the-client);
* [загрузка набора данных временного ряда из файла](#load-time-series-data-from-a-file);
* [обнаружение аномалий во всем наборе данных](#detect-anomalies-in-the-entire-data-set);
* [обнаружение состояния аномалии последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point).
* [Обнаружение точек изменения в наборе данных](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Добавьте переменную расположения Azure в конечную точку и проверьте подлинность клиента с помощью ключа.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Загрузка данных временного ряда из файла

Скачайте пример данных для этого краткого руководства с сайта [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. В браузере щелкните правой кнопкой мыши пункт **Без обработки**.
2. Выберите **Сохранить ссылку как**.
3. Сохраните файл в формате CSV в каталоге вашего приложения.

Данные временных рядов форматируются как CSV-файл и будут отправлены в API Детектора аномалий.

Загрузите свой файл данных с помощью метода `read_csv()` библиотеки Pandas и создайте переменную с пустым списком для хранения ряда данных. Выполните итерацию файла и добавьте данные в виде объекта [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python). Этот объект будет содержать метку времени и числовое значение из строк вашего CSV-файла данных.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Создайте объект [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) с вашим временным рядом и [степенью детализации](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (или периодичности) точек данных. Например, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаружение аномалий во всем наборе данных

Вызовите API для обнаружения аномалий во всех данных временного ряда с помощью метода [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) клиента. Сохраните возвращенный объект [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python). Выполните итерацию списка ответов `is_anomaly` и выведите индексы любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

Вызовите API Детектора аномалий, чтобы определить, является ли последняя точка данных аномальной, с помощью метода [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) клиента и сохраните возвращенный объект [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python). Значение `is_anomaly` в ответе представляет собой логическое значение, указывающее состояние аномалий этой точки.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Обнаружение точек изменения в наборе данных

Вызовите API для обнаружения точек изменения в данных временных рядов с помощью метода [detect_change_point()](https://go.microsoft.com/fwlink/?linkid=2090370) клиента. Сохраните полученный объект [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370). Выполните итерацию списка ответов `is_change_point` и выведите индексы любых значений `true`. Эти значения соответствуют индексам точек изменения тенденций, если они были обнаружены.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `python` и используя имя файла.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
