---
title: 'Пример: Использование функции увеличения масштаба (API распознавания лиц)'
titleSuffix: Azure Cognitive Services
description: Использование функции увеличения масштаба в API распознавания лиц.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: d8ecfb53b78277e4b0e4a85d60fb6712d0bc2292
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114832"
---
# <a name="example-use-the-large-scale-feature"></a>Пример: Использование функции для увеличения масштаба

Это руководство содержит подробное описание процессов, позволяющих перейти от существующих объектов PersonGroup и FaceList к крупным объектам LargePersonGroup и LargeFaceList соответственно. Здесь мы продемонстрируем весь процесс миграции. Для его понимания вам потребуется знакомство с объектами PersonGroup и FaceList, операцией [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) и функциями распознавания лиц. Чтобы получить дополнительные сведения по этим темам, обратитесь к концептуальному руководству по [распознаванию лиц](../concepts/face-recognition.md).

LargePersonGroup и LargeFaceList вместе называются крупномасштабными операциями. LargePersonGroup может охватывать до миллиона человек и до 248 лиц для каждого из них. LargeFaceList может охватывать до 1 млн лиц. Крупномасштабные операции аналогичны обычным PersonGroup и FaceList, но имеют ряд отличий из-за новой архитектуры. 

Примеры написаны на языке C# с использованием клиентской библиотеки API Распознавания лиц службы Azure Cognitive Services.

> [!NOTE]
> Чтобы обеспечить производительность поиска лиц для операций Identification и FindSimilar в больших масштабах, добавьте операцию Train для предварительной обработки LargeFaceList и LargePersonGroup. Время обучения составляет от нескольких секунд до получаса в зависимости от фактического размера. В период обучения можно выполнять операции Identification и FindSimilar, если перед этим уже была выполнена успешная операция обучения. Недостаток заключается в том, что новые данные добавленных людей и лиц не появятся в результатах, пока не будет завершено обучение после перехода на крупномасштабные операции.

## <a name="step-1-initialize-the-client-object"></a>Шаг 1. Инициализация объекта клиента

Если вы используете клиентскую библиотеку API "Распознавание лиц", ключ и конечная точка подписки передаются через конструктор класса FaceClient. Например:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Чтобы получить ключ подписки и соответствующую ему конечную точку, откройте Azure Marketplace через портал Azure.
Дополнительные сведения см. на странице о [подписках](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Шаг 2. Перенос кода

Этот раздел посвящен переходу с реализации на основе PersonGroup и FaceList на использование LargePersonGroup и LargeFaceList. Хотя LargePersonGroup и LargeFaceList отличаются от PersonGroup и FaceList по структуре и внутренней реализации, их интерфейсы API аналогичны для соблюдения обратной совместимости.

Перенос данных не поддерживается. Вместо этого нужно заново создать объекты LargePersonGroup или LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Переход с PersonGroup на LargePersonGroup

Переход с PersonGroup на LargePersonGroup выполняется очень просто. Они поддерживают совершенно одинаковые операции на уровне группы.

Для реализации на основе PersonGroup или Person достаточно лишь изменить пути API или класс (модуль) пакета SDK на LargePersonGroup и Person в LargePersonGroup.

Затем добавьте все лица и всех людей из PersonGroup в новую реализацию LargePersonGroup. Дополнительные сведения см. в статье о [добавлении лиц](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Переход с FaceList на LargeFaceList

| Интерфейсы API FaceList | Интерфейсы API LargeFaceList |
|:---:|:---:|
| Создание | Создание |
| Удаление | Удаление |
| Получить | Получить |
| список | список |
| Обновление | Обновление |
| - | Обучение |
| - | Get Training Status |

В таблице выше приведено сравнение операций уровня списка FaceList и LargeFaceList. Как видно, в LargeFaceList есть новые операции Train и Get Training Status, которых нет в FaceList. Обучение LargeFaceList — обязательное предварительное условие для операций [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Для FaceList обучение не требуется. В следующем фрагменте кода реализуется вспомогательная функция, которая ожидает обучения LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Раньше типичный процесс добавления лиц и выполнения FindSimilar для FaceList выглядел так:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Вот как он изменится после переноса в LargeFaceList:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Как показано выше, управление данными и выполнение FindSimilar почти идентичны. Единственным исключением является новая операция предварительной обработки Train, которую необходимо выполнить в LargeFaceList перед операцией FindSimilar.

## <a name="step-3-train-suggestions"></a>Шаг 3. Формирование предложений

Операция Train ускоряет операции [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) и [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), но время обучения увеличивается, особенно при большом масштабе. В представленной ниже таблице приведено предполагаемое время обучения для разных размеров.

| Масштаб (люди или лица) | Предполагаемое время обучения |
|:---:|:---:|
| 1000 | 1–2 с |
| 10 000 | 5–10 с |
| 100 000 | 1–2 мин |
| 1 000 000 | 10–30 мин |

Для оптимального использования крупномасштабных функций мы рекомендуем применять описанные далее стратегии.

### <a name="step-31-customize-time-interval"></a>Шаг 3.1. Настройка интервала времени

Как показано в `TrainLargeFaceList()`, можно указать период в миллисекундах для задержки неопределенно долгого процесса проверки состояния обучения. Если указать больший интервал для LargeFaceList с большим числом лиц, это сократит количество вызовов и затраты. Настройте интервал времени в соответствии с ожидаемым размером LargeFaceList.

Эта стратегия применима и к LargePersonGroup. Например, при обучении LargePersonGroup на основе данных 1 млн человек, можно присвоить `timeIntervalInMilliseconds` значение 60 000, что соответствует интервалу в 1 минуту.

### <a name="step-32-small-scale-buffer"></a>Шаг 3.2. Небольшой буфер

Поиск людей и лиц в LargePersonGroup и LargeFaceList возможен только после обучения. В сценарии динамического изменения данных новые люди и лица добавляются постоянно и должны немедленно становиться доступными для поиска. Но обучение может занимать слишком много времени. 

Чтобы устранить эту проблему, примените небольшие дополнительные объекты LargePersonGroup и LargeFaceList в качестве буфера для недавно добавленных записей. Обучение этого буфера выполняется быстрее благодаря малому размеру. По нему вы сможете немедленно организовать поиск. Используйте этот буфер в сочетании с обучением основных объектов LargePersonGroup и LargeFaceList, чтобы выполнять его реже. Например, ежедневно в полночь.

Пример рабочего процесса:

1. Создайте главную коллекцию LargePersonGroup или LargeFaceList. Создайте буферную коллекцию LargePersonGroup или LargeFaceList. Буферная коллекция предназначена только для недавно добавленных людей и лиц.
1. Добавляйте новые элементы Person и Face одновременно в основную и буферную коллекции.
1. Выполняйте обучение с небольшим интервалом только для буферной коллекции, чтобы при поиске учитывались новые записи.
1. Вызывайте операции Identification и FindSimilar одновременно по основной и буферной коллекциям. Объединяйте результаты операций.
1. Когда размер буферной коллекции достигнет определенного порога или наступит период низкой загрузки системы, создайте новую буферную коллекцию. Активируйте операцию обучения для основной коллекции.
1. Когда операция обучения для основной коллекции завершится, удалите старую буферную коллекцию.

### <a name="step-33-standalone-training"></a>Шаг 3.3. Автономное обучение

Если допустима относительно большая задержка, операцию обучения не обязательно запускать сразу после добавления новых данных. Вместо этого операцию Train можно отделить от главной логики и активировать с регулярным интервалом. Такая стратегия подходит для динамических сценариев с допустимой задержкой. Ее также можно применить в статических сценариях, чтобы дополнительно сократить частоту обучения.

Допустим, есть функция `TrainLargePersonGroup`, аналогичная `TrainLargeFaceList`. Типичная реализация изолированного обучения для LargePersonGroup через класс [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) из `System.Timers` будет выглядеть так:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Дополнительные сведения о реализации управления данными и идентификации см. в статьях с примерами [добавления лиц](how-to-add-faces.md) и [идентификации лиц на изображении](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Сводка

Из этого руководства вы узнали, как перенести существующий код PersonGroup и FaceList (без данных) в LargePersonGroup и LargeFaceList.

- LargePersonGroup и LargeFaceList работают аналогично PersonGroup и FaceList, за исключением того, что перед операцией LargeFaceList нужно выполнять операцию Train.
- Выберите стратегию обучения, соответствующую сценарию динамического обновления данных в крупномасштабных наборах данных.

## <a name="next-steps"></a>Дополнительная информация

Следуйте руководству, чтобы узнать, как добавить лица в PersonGroup или выполнить операцию Identify для PersonGroup.

- [Добавление лиц](how-to-add-faces.md)
- [Идентификация лиц на изображении](HowtoIdentifyFacesinImage.md)
