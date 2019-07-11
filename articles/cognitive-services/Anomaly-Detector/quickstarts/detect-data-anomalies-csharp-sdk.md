---
title: Краткое руководство. Обнаружение аномалий в данных временных рядов с использованием пакета SDK Детектора аномалий для .NET
titleSuffix: Azure Cognitive Services
description: Запуск обнаружения аномалий в данных временных рядов с помощью службы Детектора аномалий.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: c2a8bf10d7c279243c9f53801264ebbe9bfe72c9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503644"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Детектора аномалий для .NET

Приступите к работе с клиентской библиотекой Детектора аномалий для .NET. Чтобы установить пакет и испробовать пример кода для выполнения базовых задач, выполните приведенные здесь действия. Служба Детектора аномалий позволяет находить отклонения в данных временных рядов, автоматически используя для них наиболее подходящие модели, независимо от отрасли, сценария или объема данных.

Клиентскую библиотеку Детектора аномалий для .NET можно использовать для таких задач:

* обнаружение аномальных действий в пакетном режиме;
* обнаружение состояния аномалии последней точки данных.

[Справочная документация по API](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [примеры](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Настройка

### <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Создание приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду dotnet `new`, чтобы создать консольное приложение с именем `anomaly-detector-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: **Program.cs**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Детектора аномалий для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде пакета NuGet. 

## <a name="object-model"></a>Объектная модель

Клиент Детектора аномалий — это объект [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient), который проходит проверку подлинности в Azure с помощью объекта [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), который содержит ваш ключ. Клиент предоставляет два метода обнаружения аномалий: [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) для всего набора данных и [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) для последней точки данных. 

Данные временного ряда отправляются в виде серии [точек](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) в объекте [запроса](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). Объект `Request` содержит свойства для описания данных (например, [степень детализации](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)) и параметры для обнаружения аномалий. 

Ответ Детектора аномалий является объектом [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) или [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) (в зависимости от используемого метода). 

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Детектора аномалий для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [загрузка набора данных временного ряда из файла](#load-time-series-data-from-a-file);
* [обнаружение аномалий во всем наборе данных](#detect-anomalies-in-the-entire-data-set); 
* [обнаружение состояния аномалии последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point).

### <a name="add-the-main-method"></a>Добавление метода main

Из каталога проекта:

1. Откройте файл Program.cs в предпочитаемом редакторе или интегрированной среде разработки.
2. Затем добавьте следующие директивы `using`.

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создали переменную среды](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) для ключа Детектора аномалий с именем `ANOMALY_DETECTOR_KEY`.

В методе приложения `main()` создайте переменную для расположения вашего ресурса в Azure и свой ключ в качестве переменной среды. Если вы создали переменную среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Аутентификация клиента

В новом методе создайте экземпляр клиента с использованием конечной точки и ключа. Создайте объект [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) с помощью ключа и используйте его с конечной точкой, чтобы создать объект [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview). 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Загрузка данных временного ряда из файла

Скачайте пример данных для этого краткого руководства с сайта [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. В вашем браузере щелкните правой кнопкой мыши пункт **Без обработки**.
2. Щелкните **Сохранить ссылку как**.
3. Сохраните файл в формате CSV в каталоге вашего приложения.

Данные временных рядов форматируются как CSV-файл и будут отправлены в API Детектора аномалий.

Создайте новый метод для чтения данных временных рядов и добавьте его в объект [запроса](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview). Вызовите `File.ReadAllLines()` с использованием пути к файлу, создайте список объектов [точек](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) и удалите все символы новой строки. Извлеките значения, отделите метку даты от ее числового значения и добавьте их в новый объект `Point`. 

Создайте объект `Request` с последовательностью точек и объект `Granularity.Daily` для [степени детализации](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (или периодичности) точек данных.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаружение аномалий во всем наборе данных 

Создайте метод для вызова клиентского метода [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) с объектом `Request` и ожидайте ответ в виде объекта [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview). Если временной ряд содержит какие-либо аномалии, выполните итерацию значений ответа [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) и выведите все со значением `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояния аномалии последней точки данных

Создайте метод для вызова клиентского метода [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) с объектом `Request` и ожидайте ответ в виде объекта [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview). Проверьте атрибут [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) ответа, чтобы определить, была ли последняя отправленная точка данных аномальной. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Выполнение приложения

Перейдите к каталогу приложения и запустите приложение с помощью команды dotnet `run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с этой группой ресурсов.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Вы также можете запустить следующую команду Cloud Shell, чтобы удалить группу ресурсов и связанные с ней ресурсы. Этот процесс может занять несколько минут. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Обнаружение аномалий при потоковой передаче данных с помощью Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* [Общие сведения об API Детектора аномалий](../overview.md)
* [Рекомендации по использованию API Детектора аномалий](../concepts/anomaly-detection-best-practices.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
