---
title: Краткое руководство по использованию клиентской библиотеки Детектора аномалий (.NET)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 1a46cba6e3b74a2f8d4b63ab631830569c521291
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246372"
---
Приступите к работе с клиентской библиотекой Детектора аномалий для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба Детектора аномалий позволяет находить аномалии в данных временных рядов, автоматически применяя для них наиболее подходящие модели, независимо от отрасли, сценария или объема данных.

Клиентскую библиотеку Детектора аномалий для .NET можно использовать для таких задач:

* Обнаружение аномалий в наборе данных временного ряда с использованием пакетного запроса.
* Обнаружение состояния аномалии последней точки данных во временном ряду.

[Справочная документация по библиотеке](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Поиск кода на GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Создание ресурса Детектора аномалий"  target="_blank">созданию ресурса Детектора аномалий <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    * Для подключения приложения к API "Детектор аномалий" потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Создание приложения .NET Core

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `anomaly-detector-quickstart`. Эта команда создает простой проект Hello World с одним исходным файлом на языке C#: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```dotnetcli
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Детектора аномалий для .NET с помощью следующей команды:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

В каталоге проекта откройте файл *program.cs* и с помощью `directives` добавьте следующее:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

В методе приложения `main()` создайте переменную для расположения вашего ресурса в Azure и свой ключ в качестве переменной среды. Если вы создали переменную среды после запуска приложения, то для доступа к переменной следует закрыть и перезагрузить редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Объектная модель

Клиент Детектора аномалий — это объект [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient), который проходит проверку подлинности в Azure с помощью объекта [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), который содержит ваш ключ. Клиент предоставляет два метода обнаружения аномалий: [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) для всего набора данных и [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) для последней точки данных.

Данные временного ряда отправляются в виде ряда [точек](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) в объекте [запроса](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). Объект `Request` содержит свойства для описания данных (например, [степень детализации](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)) и параметры для обнаружения аномалий.

Ответ Детектора аномалий является объектом [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) или [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) (в зависимости от используемого метода).

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Детектора аномалий для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [загрузка набора данных временного ряда из файла](#load-time-series-data-from-a-file);
* [обнаружение аномалий во всем наборе данных](#detect-anomalies-in-the-entire-data-set);
* [обнаружение состояния аномалии последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point).

## <a name="authenticate-the-client"></a>Аутентификация клиента

В новом методе создайте экземпляр клиента с использованием конечной точки и ключа. Создайте объект [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) с помощью ключа и используйте его с конечной точкой, чтобы создать объект [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview).

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Загрузка данных временного ряда из файла

Скачайте пример данных для этого краткого руководства с сайта [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. В браузере щелкните правой кнопкой мыши пункт **Без обработки**.
2. Выберите **Сохранить ссылку как**.
3. Сохраните файл в формате CSV в каталоге вашего приложения.

Данные временных рядов форматируются как CSV-файл и будут отправлены в API Детектора аномалий.

Создайте новый метод для чтения данных временных рядов и добавьте его в объект [запроса](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview). Вызовите `File.ReadAllLines()` с использованием пути к файлу, создайте список объектов [точек](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) и удалите все символы новой строки. Извлеките значения, отделите метку даты от ее числового значения и добавьте их в новый объект `Point`.

Создайте объект `Request` с последовательностью точек и объект `Granularity.Daily` для [степени детализации](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (или периодичности) точек данных.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаружение аномалий во всем наборе данных

Создайте метод для вызова клиентского метода [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) с объектом `Request` и ожидайте ответ в виде объекта [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview). Если временной ряд содержит какие-либо аномалии, выполните итерацию значений ответа [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) и выведите все со значением `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

Создайте метод для вызова клиентского метода [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) с объектом `Request` и ожидайте ответ в виде объекта [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview). Проверьте атрибут [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) ответа, чтобы определить, была ли последняя отправленная точка данных аномальной.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение с помощью команды `dotnet run` из каталога приложения.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
