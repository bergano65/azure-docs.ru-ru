---
title: Анализ видео в режиме почти реального времени — Компьютерное зрение
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
ms.openlocfilehash: f4410d9cab5677327d2950dfdc1a093140f31708
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102278"
---
# <a name="analyze-videos-in-near-real-time"></a>Анализ видео в режиме почти реального времени

В этой статье описано, как выполнить анализ практически в реальном времени по кадрам, взятым из видеотрансляции, с помощью API компьютерного зрения. Основные элементы такого анализа:

- получение кадров из источника видео;
- выбор кадров для анализа;
- отправка этих кадров в API;
- обработка каждого результата анализа, возвращенного в результате вызова API.

Примеры в этой статье написаны на языке C#. Чтобы получить доступ к коду, на сайте GitHub перейдите на страницу [Пример анализа видеокадров](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="approaches-to-running-near-real-time-analysis"></a>Подходы к выполнению анализа в режиме почти реального времени

Вы можете выполнить анализ видеопотоков в режиме почти реального времени с помощью различных подходов. В этой статье описаны три таких подхода (по уровню сложности).

### <a name="design-an-infinite-loop"></a>Создание бесконечного цикла

Простейшая система анализа в режиме почти реального времени представляет собой бесконечный цикл. В каждой итерации этого цикла вы захватываете кадр, анализируете его и затем используете результат:

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

Если при анализе должен применяться упрощенный алгоритм на стороне клиента, такого подхода будет достаточно. Тем не менее если анализ происходит в облаке, полученная задержка означает, что вызов API может занять несколько секунд. В это время вы не записываете образы, и поток является, по сути, пассивным. Максимальная частота кадров ограничивается задержкой вызовов API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Разрешение выполнения вызовов API в параллельном режиме

Хотя простой однопоточный цикл имеет смысл использовать с упрощенным алгоритмом на стороне клиента, он плохо сочетается с задержкой вызовов API в облаке. Решение этой проблемы состоит в том, чтобы разрешить выполнять длительный вызов API одновременно с перехватом кадров. Используя язык C#, вы можете сделать это с помощью параллелизма на основе задач. Например, вы можете запустить следующий код:

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

При таком подходе каждый анализ запускается в рамках отдельной задачи. Эта задача может выполняться в фоновом режиме, пока продолжается захват новых кадров. Этот подход позволяет избежать блокировки основного потока во время ожидания возврата вызова API. Однако такой подход может иметь некоторые недостатки.
* При этом подходе теряются некоторые гарантированные функции простой версии. Следовательно, несколько вызовов API могут выполняться параллельно, а результаты могут возвращаться в неправильном порядке. 
* Это также может привести к тому, что несколько потоков одновременно введут функцию ConsumeResult(), что может представлять опасность, если функция не является потокобезопасной. 
* Наконец, этот простой код не позволяет отслеживать создаваемые задачи, поэтому исключения исчезают без уведомления. Таким образом, вам необходимо добавить "поток-получатель", который будет отслеживать задачи анализа, вызывать исключения, завершать долго выполняющиеся задачи и обеспечивать использование результатов в правильном порядке, по очереди.

### <a name="design-a-producer-consumer-system"></a>Создание конструкции "производитель-получатель"

Для последнего подхода, создавая конструкцию "производитель-получатель", вы создаете поток-производитель, который выглядит как описанный ранее бесконечный цикл. Но вместо того, чтобы использовать результаты анализа сразу же по мере их доступности, производитель просто помещает их в очередь для отслеживания.

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

Вы также создаете поток-получатель, который принимает задачи из очереди, дожидается их завершения и либо отображает результат, либо вызывает созданное исключение. С помощью очереди вы можете гарантировать, что результаты будут использоваться последовательно в правильном порядке и без ограничения максимальной частоты кадров системы.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>Реализация решения

### <a name="get-started-quickly"></a>Быстрое начало работы

Чтобы как можно быстрее запустить приложение, мы реализовали систему, описанную в предыдущем разделе. Она должна быть достаточно гибкой для реализации множества сценариев и одновременно для простоты использования. Чтобы получить доступ к коду, на сайте GitHub перейдите на страницу [Пример анализа видеокадров](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

Библиотека содержит класс `FrameGrabber`, который реализует описанную выше систему "производитель-получатель" для обработки видеокадров с веб-камеры. Пользователи могут указать точную форму вызова API, а класс с помощью событий позволяет вызывающему коду определять, когда поступает новый кадр или становится доступным новый результат анализа.

Для иллюстрации некоторых возможностей мы предоставили два примера приложений, использующих библиотеку. 

Первый пример приложения представляет собой простое консольное приложение, которое захватывает кадры с веб-камеры по умолчанию, а затем передает их в API распознавания лиц для определения лиц. Упрощенная версия приложения воспроизведена в следующем коде:

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

            // Set up a Face API call.
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

            // Tell grabber when to call the API.
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

Второй пример приложения более интересный. Он позволяет выбрать, какой API вызывать для видеокадров. Слева в приложении отображается видео в реальном времени для предварительного просмотра. Справа отображается последний результат API, наложенный на соответствующий кадр.

В большинстве режимов наблюдается видимая задержка между текущим видео с левой стороны и визуализированным анализом с правой. Эта задержка равна времени, которое занимает выполнение вызова API. Исключением является режим EmotionsWithClientFaceDetect, который позволяет выполнить обнаружение лиц локально на клиентском компьютере с помощью OpenCV перед отправкой изображений в Azure Cognitive Services. 

С помощью такого подхода вы можете сразу же визуализировать обнаруженное лицо. Затем, после возвращения вызова API, вы можете обновить эмоции. Этот пример демонстрирует возможность гибридного подхода. Это значит, что некоторая простая обработка может выполняться на клиенте, а затем с помощью API-интерфейсов Cognitive Services ее можно при необходимости дополнить более сложным анализом.

![Приложение LiveCameraSample с отображением изображения с тегами](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Интеграция примеров в базу кода

Чтобы приступить к работе с этим примером, сделайте следующее:

1. Получите ключи API-интерфейсов зрения в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). Для анализа кадров видео можно использовать следующие API-интерфейсы:
    - [API Компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API Распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Клонируйте репозиторий GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Откройте пример в Visual Studio 2015 или последующей версии, а затем выполните сборку и запуск примеров приложений.
    - Для примера BasicConsoleSample ключ API распознавания лиц содержится непосредственно в коде [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Для LiveCameraSample введите ключи в область **Параметры** приложения. Ключи будут сохраняться во всех сеансах как пользовательские данные.

Когда все будет готово к интеграции примеров, просто создайте ссылку на библиотеку VideoFrameAnalyzer из собственных проектов.

В функциях библиотеки VideoFrameAnalyzer для распознавания изображений, речи, видео и текста используются службы Azure Cognitive Services. Корпорация Майкрософт получает изображения, аудио, видео и другие данные, отправленные вами (с помощью этого приложения), и может использовать их для улучшения служб. Мы просим вас внимательно относиться к данным людей, которые ваше приложение отправляет в службы Azure Cognitive Services.

## <a name="summary"></a>Сводка

Из этой статьи вы узнали, как выполнить анализ практически в реальном времени по потоковому видео с помощью API распознавания лиц и API компьютерного зрения. Вы также узнали, как можно использовать пример кода, чтобы приступить к работе. Чтобы приступить к созданию приложения, используя бесплатные ключи API, перейдите на [страницу регистрации Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Отправляйте отзывы и предложения в [репозиторий GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Чтобы предоставить более подробные отзывы об API, перейдите на наш [сайт UserVoice](https://cognitive.uservoice.com/).

