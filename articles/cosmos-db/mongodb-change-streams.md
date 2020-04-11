---
title: Изменяйте потоки в API API Azure Cosmos DB для MongoDB
description: Узнайте, как использовать потоки изменений в API-иНоС Azure Cosmos DB для MongoDB, чтобы получить изменения, внесенные в данные.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114165"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Изменяйте потоки в API API Azure Cosmos DB для MongoDB

[Поддержка подачи изменений](change-feed.md) в API API Azure Cosmos DB для MongoDB доступна с помощью API потоков изменений. Используя API потоков изменений, приложения могут получить изменения, внесенные в коллекцию или в элементы в одном осколок. Позже вы можете предпринять дальнейшие действия на основе результатов. Изменения в элементах коллекции фиксируются в порядке их изменения времени и порядок сортировки гарантируется на клавишу осколка.

> [!NOTE]
> Чтобы использовать потоки изменений, создайте учетную запись с версией 3.6 API API Azure Cosmos DB для MongoDB или более поздней версией. Если вы запустите примеры потока изменений против `Unrecognized pipeline stage name: $changeStream` более ранней версии, вы можете увидеть ошибку.

## <a name="current-limitations"></a>Текущие ограничения

При использовании потоков изменений применяются следующие ограничения:

* Свойства `operationType` `updateDescription` и свойства еще не подтверждены в документе вывода.
* Типы `insert`операций и `replace` операции в настоящее время поддерживаются. `update` 
* Удаление операции или других событий еще не поддерживается.

Из-за этих ограничений, $match этап, $project этап, и fullDocument варианты необходимы, как показано в предыдущих примерах.

В отличие от канала изменений в API Azure Cosmos DB, нет отдельной [библиотеки процессоров Change Feed,](change-feed-processor.md) которая должна потреблять потоки изменений или необходимость в контейнере для аренды. В настоящее время не существует поддержки [триггеров Azure Functions](change-feed-functions.md) для обработки потоков изменений.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения:

* **КОД ошибки HTTP 16500** - Когда поток изменений регулируется, он возвращает пустую страницу.

* **NamespaceNotFound (OperationType Invalidate)** - Если вы запустите поток изменений в коллекции, `NamespaceNotFound` который не существует, или если коллекция удалена, то ошибка возвращается. Поскольку `operationType` свойство не может быть возвращено в `operationType Invalidate` выводном `NamespaceNotFound` документе, а не ошибка, ошибка возвращается.

## <a name="examples"></a>Примеры

Ниже приводится следующий пример, как получить потоки изменений на всех элементах коллекции. Этот пример создает курсор для просмотра элементов при их вставке, обновлении или замене. Этап, `$match` `$project` этап и `fullDocument` опция необходимы для получения потоков изменений. Просмотр операций удаления с помощью потоков изменений в настоящее время не поддерживается. В качестве обходного пути можно добавить мягкий маркер на элементах, которые удаляются. Например, можно добавить атрибут в элемент под названием "удален". Когда вы хотите удалить элемент, вы можете установить `true` "удаленный" и установить TTL на элемент. Поскольку обновление "удалено" до `true` обновления, это изменение будет видно в потоке изменений.

### <a name="javascript"></a>JavaScript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Изменения в пределах одного осколока

В следующем примере показано, как получить изменения в элементы в пределах одного осколока. Этот пример получает изменения элементов, которые имеют осколок ключа, равный "а" и осколок ключевое значение, равное "1". Можно иметь различных клиентов, читающих изменения от разных осколков параллельно.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>Дальнейшие действия

* [Используйте время, чтобы автоматически прожить до истечения срока действия данных в API API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Индексирование в API Azure Cosmos DB для MongoDB](mongodb-indexing.md)
