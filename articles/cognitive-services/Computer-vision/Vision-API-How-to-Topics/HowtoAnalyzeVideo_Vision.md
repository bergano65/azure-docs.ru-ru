---
title: 'Пример: Анализ видео в режиме реального времени с помощью API компьютерного зрения'
titleSuffix: Azure Cognitive Services
description: Узнайте, как выполнить анализ практически в реальном времени по кадрам, взятым из видеотрансляции, с помощью API компьютерного зрения.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859072"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Анализ видео в реальном времени

В этом руководстве показано, как выполнить анализ практически в реальном времени по кадрам, взятым из видеотрансляции. Базовые операции этой системы:

- получение кадров из источника видео;
- выбор кадров для анализа;
- отправка этих кадров в API;
- обработка каждого результата анализа, возвращенного в результате вызова API.

Эти примеры написаны на C#. Код можно найти на GitHub: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Подход

Есть несколько способов выполнить анализ видеопотоков практически в реальном времени. Начнем с описания трех подходов (по уровню сложности).

### <a name="a-simple-approach"></a>Простой подход

Простейшая система анализа практически в реальном времени представляет собой бесконечный цикл, где в каждой итерации мы захватываем кадр, анализируем его и затем используем результат:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Если при анализе применялся упрощенный алгоритм на стороне клиента, такого подхода будет достаточно. Но когда анализ выполняется в облаке, возникающая задержка означает, что вызов API может занимать несколько секунд, в течение которых мы не захватываем изображения, и наш поток фактически не выполняет никаких действий. Максимальная частота кадров ограничивается задержкой вызовов API.

### <a name="parallelizing-api-calls"></a>Организация параллельных вызовов API

Хотя простой однопоточный цикл имеет смысл использовать с упрощенным алгоритмом на стороне клиента, он плохо сочетается с задержкой при вызовах API в облаке. Решение этой проблемы состоит в том, чтобы разрешить выполнять длительные вызовы API одновременно с перехватом кадров. В C# это достигается с помощью параллелизма на основе задач, например:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

С помощью этого подхода каждый анализ запускается в рамках отдельной задачи, которая может выполняться в фоновом режиме, пока продолжается захват новых кадров. Это позволяет избежать блокировки основного потока во время ожидания возврата вызова API, но при этом теряются некоторые гарантированные функции простой версии, следовательно, несколько вызовов API могут выполняться параллельно, а результаты могут возвращаться в неправильном порядке. Также с помощью этого подхода несколько потоков могут одновременно использовать функцию ConsumeResult(), что может представлять опасность, если эта функция не является потокобезопасной. Наконец, этот простой код не позволяет отслеживать создаваемые задачи, поэтому исключения будут исчезать без уведомления. Таким образом, последняя составляющая — добавить "поток-получатель", который будет отслеживать задачи анализа, вызывать исключения, завершать долго выполняющиеся задачи и обеспечивать использование результатов в правильном порядке, по одному за раз.

### <a name="a-producer-consumer-design"></a>Конструкция "производитель-получатель"

В нашей итоговой системе "производитель-получатель" есть поток-производитель, который похож на описанный ранее бесконечный цикл. Но вместо того, чтобы использовать результаты анализа сразу же по мере их доступности, производитель просто помещает их в очередь для отслеживания.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

У нас также есть поток-получатель, который принимает задачи из очереди, дожидается их завершения и либо отображает результат, либо вызывает созданное исключение. С помощью очереди мы можем гарантировать, что результаты будут использоваться последовательно в правильном порядке и без ограничения максимальной частоты кадров системы.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Реализация решения

### <a name="getting-started"></a>Начало работы

Чтобы как можно быстрее запустить приложение, мы реализовали описанную выше систему, спроектировав ее достаточно гибкой для реализации множества сценариев и одновременно для простоты использования. Получить код можно отсюда: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Библиотека содержит класс FrameGrabber, который реализует описанную выше систему "производитель-получатель" для обработки видеокадров с веб-камеры. Пользователь может указать точную форму вызова API, а класс с помощью событий позволяет вызывающему коду определять, когда поступает новый кадр или становится доступным новый результат анализа.

Для иллюстрации некоторых возможностей есть два примера приложений, использующих библиотеку. Первый — это простое консольное приложение, упрощенная версия которого воспроизведена ниже. Приложение захватывает кадры с установленной по умолчанию веб-камеры и отправляет их в API распознавания лиц для обработки.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Второй пример приложения более интересный. Он позволяет выбрать, какой API вызывать для видеокадров. Слева в приложении отображается видео в реальном времени для предварительного просмотра, справа — последний результат API, наложенный на соответствующий кадр.

В большинстве режимов будет наблюдаться видимая задержка между текущим видео с левой стороны и визуализированным анализом с правой. Эта задержка равна времени, которое занимает выполнение вызова API. Исключением является режим EmotionsWithClientFaceDetect, который выполняет обнаружение лиц локально на клиентском компьютере с помощью OpenCV перед отправкой изображений в Cognitive Services. Таким образом мы можем сразу же визуализировать обнаруженное лицо, а затем обновить эмоции после возвращения вызова API. Этот пример демонстрирует возможность гибридного подхода, когда некоторая простая обработка может выполняться на клиенте, а затем с помощью API-интерфейсов Cognitive Services ее можно при необходимости дополнить более сложным анализом.

![Снимок экрана приложения LiveCameraSample, на котором показано изображение с тегами](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Интеграция в базу кода

Чтобы приступить к работе с этим примером, сделайте следующее:

1. Получите ключи API-интерфейсов зрения в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). Для анализа кадров видео можно использовать следующие API-интерфейсы:
    - [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API Распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Клонируйте репозиторий GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Откройте пример в Visual Studio 2015 или последующей версии, выполните сборку и запуск примеров приложений.
    - Для примера BasicConsoleSample ключ API распознавания лиц содержится непосредственно в коде [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Для LiveCameraSample ключи следует ввести в области "Параметры" приложения. Они будут сохраняться во всех сеансах как пользовательские данные.

Когда все будет готово к интеграции, **просто создайте ссылку на библиотеку VideoFrameAnalyzer из собственных проектов**.

В функциях библиотеки VideoFrameAnalyzer для распознавания изображений, речи, видео или текста используются службы Azure Cognitive Services. Корпорация Майкрософт будет получать изображения, аудио, видео и другие данные, отправленные вами (с помощью этого приложения), чтобы использовать их для улучшения служб. Мы просим вас внимательно относиться к данным людей, которые ваше приложение отправляет в службы Azure Cognitive Services.

## <a name="summary"></a>Сводка

В этом руководстве описано, как запустить анализ потокового видео практически в реальном времени с использованием API распознавания лиц и компьютерного зрения и приступить к работе с использованием примеров кода. Вы можете создать свое приложения, используя бесплатные ключи API на [странице регистрации Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Отправляйте отзывы и предложения в [репозиторий GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), а также развернутые идеи об API на наш [сайт UserVoice](https://cognitive.uservoice.com/).

