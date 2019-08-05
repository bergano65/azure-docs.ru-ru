---
title: Краткое руководство. Обнаружение аномалий данных с помощью клиентской библиотеки Детектора аномалий для Python
titleSuffix: Azure Cognitive Services
description: API Детектора аномалий используется для обнаружения отклонений в ряде данных как в пакетном режиме, так и при потоковой передаче.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: b78d19841bdca100211378f71e45a41dd37aad28
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639342"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Краткое руководство. Клиентская библиотека Детектора аномалий для Python

Приступите к работе с клиентской библиотекой Детектора аномалий для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба Детектора аномалий позволяет находить аномалии в данных временных рядов, автоматически применяя для них наиболее подходящие модели, независимо от отрасли, сценария или объема данных.

Клиентскую библиотеку Детектора аномалий для Python можно использовать для таких задач:

* Обнаружение аномалий в наборе данных временного ряда с использованием пакетного запроса.
* Обнаружение состояния аномалии последней точки данных во временном ряду.

[Справочная документация по библиотеке](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Пакет (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Примеры](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)
* [Библиотека анализа данных Pandas](https://pandas.pydata.org/).
 
## <a name="setting-up"></a>Настройка

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) для ключа с именем `ANOMALY_DETECTOR_KEY`.

### <a name="create-a-new-python-application"></a>Создание приложения Python

 Создайте приложение Python в предпочитаемом редакторе или среде интегрированной разработки. Затем импортируйте приведенные ниже библиотеки.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Создайте переменные для ключа в качестве переменной среды, путь к файлу данных временного ряда, а также расположение Azure для вашей подписки. Например, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Объектная модель

Клиент Детектора аномалий — это объект [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python), который выполняет проверку подлинности в Azure с помощью вашего ключа. Клиент предоставляет два метода обнаружения аномалий: [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) для всего набора данных и [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) для последней точки данных. 

Данные временного ряда отправляются в виде ряда [точек](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) в объекте [запроса](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). Объект `Request` содержит свойства для описания данных (например, [степень детализации](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)) и параметры для обнаружения аномалий. 

Ответ Детектора аномалий является объектом [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) или [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) (в зависимости от используемого метода). 

## <a name="code-examples"></a>Примеры кода 

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Детектора аномалий для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [загрузка набора данных временного ряда из файла](#load-time-series-data-from-a-file);
* [обнаружение аномалий во всем наборе данных](#detect-anomalies-in-the-entire-data-set); 
* [обнаружение состояния аномалии последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point).

### <a name="authenticate-the-client"></a>Аутентификация клиента

Добавьте переменную расположения Azure в конечную точку и проверьте подлинность клиента с помощью ключа.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Загрузка данных временного ряда из файла

Скачайте пример данных для этого краткого руководства с сайта [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. В браузере щелкните правой кнопкой мыши пункт **Без обработки**.
2. Выберите **Сохранить ссылку как**.
3. Сохраните файл в формате CSV в каталоге вашего приложения.

Данные временных рядов форматируются как CSV-файл и будут отправлены в API Детектора аномалий.

Загрузите свой файл данных с помощью метода `read_csv()` библиотеки Pandas и создайте переменную с пустым списком для хранения ряда данных. Выполните итерацию файла и добавьте данные в виде объекта [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Этот объект будет содержать метку времени и числовое значение из строк вашего CSV-файла данных. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Создайте объект [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) с вашим временным рядом и [степенью детализации](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (или периодичности) точек данных. Например, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаружение аномалий во всем наборе данных 

Вызовите API для обнаружения аномалий во всех данных временного ряда с помощью метода [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) клиента. Сохраните возвращенный объект [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python). Выполните итерацию списка ответов `is_anomaly` и выведите индексы любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояния аномалии последней точки данных

Вызовите API Детектора аномалий, чтобы определить, является ли последняя точка данных аномальной, с помощью метода [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) клиента и сохраните возвращенный объект [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python). Значение `is_anomaly` в ответе представляет собой логическое значение, указывающее состояние аномалий этой точки.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение в среде интегрированной разработки или в командной строке вместе с командой `python` и именем вашего файла.
 
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с этой группой ресурсов.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Обнаружение аномалий при потоковой передаче данных с помощью Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* [Общие сведения об API Детектора аномалий](../overview.md)
* [Рекомендации по использованию API Детектора аномалий](../concepts/anomaly-detection-best-practices.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
