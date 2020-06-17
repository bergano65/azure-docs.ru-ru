---
title: Модель извлечения данных канала изменений
description: Сведения об использовании модели извлечения Azure Cosmos DB для чтения веб-канала изменений и о различиях между моделью извлечения и обработчиком веб-канала изменений
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: c47016d0b82a4e4ed084f5d82394d91fd2b46be1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697710"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Модель извлечения канала изменений в Azure Cosmos DB

С помощью модели извлечения веб-канала изменений вы можете обрабатывать данные веб-канала изменений Azure Cosmos DB в удобном для себя темпе. Как и в случае с уже изученным [обработчиком веб-канала изменений](change-feed-processor.md), модель извлечения веб-канала изменений можно применить для параллельной обработки изменений в нескольких потребителях веб-канала изменений.

> [!NOTE]
> Модель извлечения веб-канала изменений в настоящее время предоставляется только в [пакете SDK .NET для Azure Cosmos DB и в режиме предварительной версии](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview). Для других версий пакета SDK пока недоступна даже предварительная версия.

## <a name="consuming-an-entire-containers-changes"></a>Обработка изменений для всего контейнера

Вы можете создать `FeedIterator` для обработки веб-канала изменений с помощью модели извлечения. При первоначальном создании `FeedIterator` можно указать необязательный параметр `StartTime` в `ChangeFeedRequestOptions`. Если он не указан, для `StartTime` используется текущее значение времени.

Для `FeedIterator` возможны два варианта. Помимо приведенных ниже примеров, которые возвращают объекты сущностей, для получения ответа можно использовать поддержку `Stream`. Потоки позволяют считывать данные без предварительной десериализации, что экономит ресурсы клиента.

Следующий пример получает `FeedIterator`, который возвращает объекты сущности, роль которых здесь выполняет объект `User`:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Этот пример получает `FeedIterator`, который возвращает `Stream`:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

С помощью `FeedIterator` вы можете легко обработать веб-канал изменений для всего контейнера в удобном для себя темпе. Ниже приведен пример:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator<User>();

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Обработка изменений по ключу секции

В некоторых случаях требуется обработка изменений только для определенного ключа раздела. Вы можете получить `FeedIterator` для определенного ключа секции и обработать изменения так же, как и для всего контейнера.

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator<User>(new PartitionKey("myPartitionKeyValueToRead"));

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Использование FeedRange для параллелизации

[Обработчик канала изменений](change-feed-processor.md) автоматически распределяет нагрузку между несколькими потребителями. В модели извлечения веб-канала изменений можно применить `FeedRange` для параллелизации обработки веб-канала изменений. `FeedRange` представляет определенный диапазон значений ключа секции.

Следующий пример демонстрирует получение списка диапазонов для контейнера.

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

При получении списка FeedRange для контейнера вы получите один `FeedRange` для каждой [физической секции](partition-data.md#physical-partitions).

С помощью `FeedRange` можно создать `FeedIterator` для параллелизации обработки веб-канала изменений на нескольких компьютерах или потоках. В отличие от предыдущего примера, в котором показано, как получить один `FeedIterator` для всего контейнера, с помощью `FeedRange` вы можете получить несколько FeedIterator и обрабатывать веб-канал изменений параллельно.

Если вы хотите использовать FeedRange, создайте процесс оркестрации, который получает экземпляры FeedRange и распространяет их по компьютерам. Это распространение можно выполнить следующими способами.

* Используйте `FeedRange.ToJsonString` и передайте это строковое значение. Потребители могут использовать это значение для `FeedRange.FromJsonString`.
* Если распределение выполняется в том же процессе, передавайте ссылку на объект `FeedRange`.

Ниже приведен пример чтения из начала веб-канала изменений для контейнера с использованием двух гипотетических компьютеров, работающих параллельно.

Компьютер 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
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

Вы можете сохранить текущее расположение `FeedIterator`, создав маркер продолжения. Маркер продолжения — это строковое значение, которое отслеживает последние изменения, обработанные в FeedIterator. Это позволяет позднее возобновить `FeedIterator` с той же позиции. Следующий код постоянно считывает веб-канал изменений с момента создания контейнера. Когда не останется доступных изменений, он сохраняет маркер продолжения, чтобы позднее возобновить обработку этого веб-канала изменений.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

Пока существует контейнер Cosmos, маркер продолжения FeedIterator считается действительным.

## <a name="comparing-with-change-feed-processor"></a>Сравнение с обработчиком канала изменений

Во многих сценариях веб-канал изменений можно обрабатывать как с помощью [обработчика веб-канала изменений](change-feed-processor.md), так и модели извлечения. Маркеры продолжения в модели извлечения и контейнер аренды в обработчике веб-канала изменений выполняют роли "закладок", то есть обозначают последний обработанный элемент (или пакет элементов) в веб-канале изменений.
Но вы не сможете преобразовать маркер продолжения в контейнер аренды (или наоборот).

Мы рекомендуем применять модель извлечения в следующих ситуациях:

- считывание изменений по определенному ключу секции;
- необходимость в управлении скоростью, с которой клиент получает изменения для обработки;
- однократный доступ к существующим данным в веб-канале изменений (например, для переноса данных).

Ниже перечислены некоторые ключевые различия между обработчиком веб-канала изменений и моделью извлечения.

|  | Обработчик канала изменений| Модель извлечения |
| --- | --- | --- |
| Отслеживание текущей точки обработки в веб-канале изменений | Аренда (хранится в контейнере Azure Cosmos DB) | Маркер продолжения (сохраненный в памяти или вручную) |
| Возможность воспроизведения прошлых изменений | Да, с моделью отправки | Да, с моделью извлечения|
| Опрос предстоящих изменений | Автоматическая проверка наличия изменений по указанному пользователем `WithPollInterval` | Вручную |
| Обработка изменений от целого контейнера | Да, с автоматической параллелизацией по нескольким потокам или компьютерам для одного контейнера| Да, с параллелизацией вручную через FeedToken |
| Обработка изменений по одному ключу секции | Не поддерживается | Да|
| Уровень поддержки | Общедоступная версия | Preview (Предварительный просмотр) |

## <a name="next-steps"></a>Дальнейшие действия

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Использование обработчика для канала изменений](change-feed-processor.md)
* [Активация Функций Azure](change-feed-functions.md)