---
title: Изменение потоков в API-интерфейсе Azure Cosmos DB для MongoDB
description: Узнайте, как использовать потоки изменений в API Azure Cosmos DB для MongoDB, чтобы получить изменения, внесенные в данные.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872145"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Изменение потоков в API-интерфейсе Azure Cosmos DB для MongoDB

Поддержка [веб-канала изменений](change-feed.md) в API Azure Cosmos DB для MongoDB доступна с помощью API потоков изменений. С помощью API изменений потоков приложения могут получать изменения, внесенные в коллекцию или элементы одного сегмента. Позже можно будет выполнить дальнейшие действия на основе результатов. Изменения элементов в коллекции фиксируются в порядке их изменения, и порядок сортировки гарантирован на каждом ключе сегмента.

> [!NOTE]
> Чтобы использовать потоки изменений, создайте учетную запись с API Azure Cosmos DB версии 3,6 для MongoDB или более поздней версии. Если запустить примеры потока изменений для более ранней версии, может появиться `Unrecognized pipeline stage name: $changeStream` сообщение об ошибке.

## <a name="current-limitations"></a>Текущие ограничения

При использовании потоков изменений применяются следующие ограничения.

* Свойства `operationType` и `updateDescription` пока не поддерживаются в выходном документе.
* Типы `insert`операций `update`, и `replace` сейчас поддерживаются. 
* Операция удаления или другие события пока не поддерживаются.

Из-за этих ограничений требуется $match этап, $project этап и параметры Фуллдокумент, как показано в предыдущих примерах.

В отличие от веб-канала изменений в API SQL Azure Cosmos DB, не существует отдельной [библиотеки обработчиков каналов изменений](change-feed-processor.md) для использования потоков изменений или для контейнера аренды. В настоящее время [триггеры функций Azure](change-feed-functions.md) не поддерживаются для обработки потоков изменений.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения:

* **Код ошибки HTTP 16500** — когда поток изменений регулируется, возвращается пустая страница.

* **Намеспаценотфаунд (OperationType unvalidate)** — при запуске потока изменений в несуществующей коллекции или при удалении коллекции возвращается `NamespaceNotFound` ошибка. Поскольку `operationType` свойство не может быть возвращено в выходном документе, а не `operationType Invalidate` на ошибку, `NamespaceNotFound` возвращается ошибка.

## <a name="examples"></a>Примеры

В следующем примере показано, как получить потоки изменений для всех элементов в коллекции. В этом примере создается курсор для просмотра элементов при их вставке, обновлении или замене. Для `$match` получения потоков `$project` изменений требуется этап `fullDocument` , этап и параметр. Наблюдение за операциями удаления с помощью потоков изменений сейчас не поддерживается. В качестве обходного решения можно добавить мягкий маркер для удаляемых элементов. Например, можно добавить атрибут в элемент с именем Deleted. Если вы хотите удалить элемент, можно присвоить параметру "Deleted" `true` значение и задать TTL для элемента. Так как обновление "Deleted `true` " до является обновлением, это изменение будет отображаться в потоке изменений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="c"></a>[C#](#tab/csharp)

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

## <a name="changes-within-a-single-shard"></a>Изменения в пределах одного сегмента

В следующем примере показано, как получить изменения элементов в пределах одного сегмента. Этот пример возвращает изменения элементов, у которых ключ сегмента равен "a", а значение ключа сегмента равно "1". Разные клиенты могут одновременно считывать изменения из разных сегментов.

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

* [Используйте срок жизни для автоматического истечения срока действия данных в API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Индексирование в API Azure Cosmos DB для MongoDB](mongodb-indexing.md)
