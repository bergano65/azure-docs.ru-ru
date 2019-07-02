---
title: Краткое руководство. Обнаруживать аномалии данных с помощью библиотеки обнаружения аномалий и Python | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Используйте API обнаружения аномалий для обнаружения отклонений в ряду данных в пакетном режиме или при потоковой передаче данных.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503678"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Краткое руководство. Средства обнаружения аномалий: клиентская библиотека для Python

Начало работы с клиентской библиотекой средства обнаружения аномалий для .NET. Выполните следующие действия, чтобы установить пакет и попробуйте выполнить пример кода для решения базовых задач. Служба обнаружения аномалий позволяет найти аномалии в данных временных рядов, автоматически с использованием наиболее подгонки моделей, независимо от отрасли, сценария или объем данных.

## <a name="key-concepts"></a>Основные понятия

Использование клиентской библиотеки обнаружения аномалий для Python:

* Обнаруживать аномалии, на протяжении вашего набора данных временного ряда, в виде пакетного запроса
* Определить состояние аномалий последние точки данных в вашей временных рядов

[Справочная документация по библиотеке](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [пакета (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [примеры](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Технические условия

* Подписка Azure — [создать бесплатно](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Библиотеки анализа данных Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Настройка

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса обнаружения аномалий

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Установить клиентскую библиотеку

После установки Python, можно установить клиентскую библиотеку с:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Объектная модель

Клиент обнаружения аномалий [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) объект, который выполняет проверку подлинности в Azure с помощью ключа. Клиент предоставляет два метода обнаружения аномалий: Весь набор данных с помощью [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)и с новейшими данными точки с использованием [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Данные временных рядов отправляется как ряд [точки](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) в [запроса](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) объекта. `Request` Объект содержит свойства для описания данных ([гранулярности](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) например) и параметры для обнаружения аномалий. 

Ответ обнаружения аномалий — [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) или [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) объект в зависимости от метода, используемого. 

## <a name="getting-started"></a>Приступая к работе

Создание приложения Python в предпочитаемом редакторе или интегрированной среде разработки. Затем добавьте следующие объявления импорта в файл. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создания переменной среды](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) ключа обнаружения аномалий, с именем `ANOMALY_DETECTOR_KEY`.

Создайте переменные для ключа как переменную среды, путь к файл время рядов данных, а также расположение azure для вашей подписки. Например, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Примеры кода 

Эти фрагменты кода показано, как выполнить следующую операцию с клиентской библиотекой средства обнаружения аномалий для .NET:

* [Проверки подлинности клиента](#authenticate-the-client)
* [Загрузить набор данных временного ряда из файла](#load-time-series-data-from-a-file)
* [Обнаруживать аномалии в весь набор данных](#detect-anomalies-in-the-entire-data-set) 
* [Обнаружение аномалий состояние последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Проверки подлинности клиента

Добавьте переменную расположение azure в конечную точку и проверки подлинности клиента с помощью ключа.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Загрузка данных временных рядов из файла

Загрузить данные для примера в этом кратком руководстве из [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. В браузере, щелкните правой кнопкой мыши **Raw**
2. Нажмите кнопку **ссылка "Сохранить как"**
3. Сохраните файл в каталог приложения в файл CSV.

Этот данных временных рядов имеет формат CSV-файл и будет отправляться API обнаружения аномалий.

Загрузить файл данных с помощью Pandas библиотеки `read_csv()` метод и сделать переменную пустой список для хранения ряда данных. Просмотр файла и добавить данные в виде [точки](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) объекта. Этот объект будет содержать метку времени и числовое значение из строки данных CSV-файла. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Создание [запроса](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) объект с вашей временных рядов и [гранулярности](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (или периодичности) из точек данных. Например, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаруживать аномалии в весь набор данных 

Вызов API для обнаружения аномалий через весь временным рядам, с помощью клиента [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) метод. Store возвращенного [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) объекта. Итерации до получения ответа `is_anomaly` списка и печать индекса любого `true` значения. Эти значения соответствуют индекс точки данных подозрительной активности, если любой объект найден.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение аномалий состояние последней точки данных

Вызвать API обнаружения аномалий, чтобы определить, является ли последняя точка данных об аномалии, с помощью клиента [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) метод и хранилища, возвращенного [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) объекта. Ответ `is_anomaly` значение — логическое значение, указывающее состояние аномалий этой точки.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение в среде IDE или в командной строке с `python` команда и имя файла.
 
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, можно удалить ресурс или группу ресурсов. Удаление группы ресурсов также удаляет другие ресурсы, связанные с группой ресурсов.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Можно также выполнить следующую команду оболочки облака, чтобы удалить группу ресурсов и связанные с ней ресурсы. Это может занять несколько минут. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Потоковая передача обнаружение аномалий с помощью Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Что такое [API обнаружения аномалий?](../overview.md)
* [Советы и рекомендации](../concepts/anomaly-detection-best-practices.md) при использовании API обнаружения аномалий.
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
