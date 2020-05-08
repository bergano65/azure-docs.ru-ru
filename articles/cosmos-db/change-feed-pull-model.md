---
title: Изменить модель извлечения веб-канала
description: Сведения об использовании модели извлечения веб-канала изменений Azure Cosmos DB для чтения веб-канала изменений и различий между моделью извлечения и обработчиком веб-канала изменений
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 2854e3d92462ced3958afd1cf1e7e99d7e9892f6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984685"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Изменить модель извлечения веб-канала в Azure Cosmos DB

С помощью модели извлечения веб-канала изменений вы можете использовать веб-канал изменений Azure Cosmos DB в удобном для вас темпе. Так как вы уже можете работать с [обработчиком веб-канала изменений](change-feed-processor.md), можно использовать модель извлечения веб-канала изменений для параллелизации обработки изменений в нескольких потребителях веб-канала изменений.

> [!NOTE]
> Модель извлечения веб-канала изменений в настоящее время доступна в [предварительной версии только в пакете SDK для .net Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . Предварительная версия еще недоступна для других версий пакета SDK.

## <a name="consuming-an-entire-containers-changes"></a>Использование изменений всего контейнера

Можно создать для обработки `FeedIterator` веб-канала изменений с помощью модели извлечения. При первоначальном создании `FeedIterator`можно указать необязательный `StartTime` объект в. `ChangeFeedRequestOptions` Если параметр `StartTime` left не указан, будет использоваться текущее время.

`FeedIterator` Имеет две разновидности. Помимо приведенных ниже примеров, возвращающих объекты сущностей, можно также получить ответ с `Stream` поддержкой. Потоки позволяют считывать данные без их десериализации, сохраняя на клиентских ресурсах.

Ниже приведен пример получения `FeedIterator` `User` объекта, который возвращает объекты сущности, в данном случае объект:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Ниже приведен пример получения объекта `FeedIterator` , который возвращает: `Stream`

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

С помощью `FeedIterator`можно легко обработать весь веб-канал изменений контейнера в удобном для вас темпе. Ниже приведен пример:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Использование изменений ключа секции

В некоторых случаях может потребоваться обработка только определенных изменений ключа раздела. Вы можете получить `FeedIterator` для определенного ключа секции и обработать изменения так же, как и для всего контейнера:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Использование Фидранже для параллелизации

В [обработчике веб-канала изменений](change-feed-processor.md)работа автоматически распределяется между несколькими потребителями. В модели извлечения веб-канала изменений можно использовать `FeedRange` для параллелизации обработки веб-канала изменений. `FeedRange` Представляет диапазон значений ключа секции.

Ниже приведен пример, демонстрирующий получение списка диапазонов для контейнера.

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

При получении списка Фидранжес для контейнера вы получите по одному `FeedRange` на [физический раздел](partition-data.md#physical-partitions).

`FeedRange`С помощью можно создать для параллелизации обработки веб `FeedIterator` -канала изменений на нескольких компьютерах или в потоках. В отличие от предыдущего примера, в котором показано, как получить `FeedIterator` один объект для всего контейнера, можно использовать `FeedRange` для получения нескольких фидитераторс, которые могут обрабатывать веб-канал изменений параллельно.

Если вы хотите использовать Фидранжес, необходимо иметь процесс Orchestrator, который получает Фидранжес и распространяет их на эти компьютеры. Это распространение может быть следующим:

* Использование `FeedRange.ToJsonString` и распространение этого строкового значения. Потребители могут использовать это значение с`FeedRange.FromJsonString`
* Если распределение выполняется в процессе, передача ссылки на `FeedRange` объект.

Ниже приведен пример, демонстрирующий чтение с начала веб-канала изменений контейнера с использованием двух гипотетических отдельных машин, считываемых параллельно.

Компьютер 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Компьютер 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Сохранение маркеров продолжения

Вы можете сохранить расположение `FeedIterator` , создав маркер продолжения. Токен продолжения — это строковое значение, которое отслеживает изменения, внесенные в последние обработанные Фидитератор. Это позволяет `FeedIterator` впоследствии возобновить работу на этом этапе. Следующий код будет считывать через веб-канал изменений после создания контейнера. После того как больше нет доступных изменений, он сохранит маркер продолжения, чтобы впоследствии можно было возобновить использование веб-канала изменений.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = orders.ContinuationToken;

   foreach (User user in Users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Сравнение с обработчиком веб-канала изменений

Во многих сценариях канал изменений может быть обработан с помощью [обработчика веб-канала изменений](change-feed-processor.md) или модели извлечения. Маркеры продолжения модели извлечения и контейнер аренды обработчика веб-канала изменений являются "закладками" для последнего обработанного элемента (или пакета элементов) в веб-канале изменений.
Однако нельзя преобразовать маркеры продолжения в контейнер аренды (или наоборот).

В этих сценариях рекомендуется использовать модель извлечения.

- Необходимо выполнить однократный доступ к существующим данным в веб-канале изменений
- Требуется только чтение изменений из определенного ключа секции
- Вы не хотите, чтобы модель push-уведомлений использовалась в удобном для вас темпе.

Ниже приведены некоторые ключевые отличия между обработчиком веб-канала изменений и моделью извлечения.

|  | Обработчик канала изменений| Модель извлечения |
| --- | --- | --- |
| Отслеживание текущей точки в обработке веб-канала изменений | Аренда (хранится в контейнере Azure Cosmos DB) | Токен продолжения (сохраненный в памяти или сохраненный вручную) |
| Возможность воспроизведения прошлых изменений | Да, с моделью push-уведомлений | Да, с моделью извлечения|
| Опрос для будущих изменений | Автоматически проверяет наличие изменений на основе указанных пользователем`WithPollInterval` | Вручную |
| Обработка изменений из всего контейнера | Да, и автоматически параллелить в нескольких потоках или компьютерах, использующих один и тот же контейнер| Да, и вручную выполнить параллелизации с помощью Фидтокенс |
| Обработка изменений только из одного ключа секции | Не поддерживается | Да|
| Уровень поддержки | Общедоступная версия | Предварительный просмотр |

## <a name="next-steps"></a>Дальнейшие действия

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Использование обработчика веб-канала изменений](change-feed-processor.md)
* [Активация Функций Azure](change-feed-functions.md)