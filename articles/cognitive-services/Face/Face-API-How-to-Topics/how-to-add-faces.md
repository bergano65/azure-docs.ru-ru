---
title: 'Пример: Добавление лиц в PersonGroup — API распознавания лиц'
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как добавить большое число людей и лиц в объект PersonGroup с помощью API "Распознавание лиц" в Azure Cognitive Services.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 2f8a6272b02aea5948be79ddf72d105c4f72bb33
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744244"
---
# <a name="add-faces-to-a-persongroup"></a>Добавление лиц в PersonGroup

В этом руководстве показано, как добавить большое число людей и лиц в объект PersonGroup. Эта же стратегия применяется к объектам LargePersonGroup, FaceList и LargeFaceList. Пример написан на языке C# с использованием клиентской библиотеки .NET для API распознавания лиц службы Azure Cognitive Services.

## <a name="step-1-initialization"></a>Шаг 1. Инициализация

В следующем коде объявляется несколько переменных и реализуется вспомогательная функция для планирования запросов на добавление лиц:

- `PersonCount` — общее количество людей.
- `CallLimitPerSecond` — максимальное количество вызовов в секунду в соответствии с категорией подписки.
- `_timeStampQueue` — очередь для записи меток времени запросов.
- `await WaitCallLimitPerSecondAsync()` — ожидание момента, когда можно будет отправить следующий запрос.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Шаг 2. Авторизация вызова API

Если вы используете клиентскую библиотеку, вы должны передать в конструктор класса **FaceClient** ключ подписки. Например:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Чтобы получить ключ подписки, откройте Azure Marketplace через портал Azure. Дополнительные сведения см. на странице о [подписках](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Шаг 3. Создание PersonGroup

Для сохранения данных людей лиц создается PersonGroup с именем MyPersonGroup.
Время запроса ставится в очередь `_timeStampQueue` для обеспечения общей проверки.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Шаг 4. Создание данных людей для PersonGroup

Данные людей создаются одновременно. Кроме того, во избежание превышения предельного количества вызовов применяется `await WaitCallLimitPerSecondAsync()`.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Шаг 5. Добавление лиц людей

Лица, добавляемые в данные разных людей, обрабатываются одновременно. Лица, добавляемые в данные одного человека, обрабатываются последовательно.
Опять же, во избежание превышения предельной частоты запросов вызывается `await WaitCallLimitPerSecondAsync()`.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Сводка

В этом руководстве вы узнали о процессе создания PersonGroup с большим числом людей и лиц. Несколько напоминаний:

- Эта стратегия также применима к FaceList и LargePersonGroup.
- Добавление или удаление лиц для разных объектов FaceList или данных разных людей в LargePersonGroup обрабатывается параллельно.
- Добавление или удаление лиц для одного объекта FaceList или человека в LargePersonGroup обрабатывается параллельно.
- Для простоты в данном руководстве не рассматривается процедура обработки возможных исключений. Если вы хотите повысить надежность, следует применить соответствующую политику повтора.

Мы объяснили и продемонстрировали следующие возможности:

- создание объектов PersonGroup с помощью API [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244);
- назначение людей в PersonGroup с помощью API [Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c);
- Добавление лиц в данные людей с помощью API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="related-topics"></a>Связанные разделы

- [Идентификация лиц на изображении](HowtoIdentifyFacesinImage.md)
- [Определение лиц на изображении](HowtoDetectFacesinImage.md)
- [Использование функции увеличения масштаба](how-to-use-large-scale.md)
