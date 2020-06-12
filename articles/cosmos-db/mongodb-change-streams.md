---
title: Потоки изменений в API Azure Cosmos DB для MongoDB
description: Узнайте, как использовать потоки изменений в API Azure Cosmos DB для MongoDB, чтобы получать изменения, внесенные в данные.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: cc6b74a56d2a538d35e324090832e6c7e03e609f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647305"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Потоки изменений в API Azure Cosmos DB для MongoDB

Поддержка [канала изменений](change-feed.md) в API Azure Cosmos DB для MongoDB доступна с помощью API потоков изменений. С помощью API потоков изменений приложения могут получать изменения, внесенные в коллекцию или элементы в пределах одного сегмента. На основе полученных результатов можно будет выполнять дальнейшие действия. Изменения элементов в коллекции фиксируются в порядке времени их изменения, и в каждом ключе сегмента обязательно соблюдается порядок сортировки.

[!NOTE]
Чтобы использовать потоки изменений, создайте учетную запись с API Azure Cosmos DB для MongoDB версии 3.6 или более поздней версии. При запуске примеров потока изменений в более ранней версии может появиться сообщение об ошибке `Unrecognized pipeline stage name: $changeStream`.

## <a name="current-limitations"></a>Текущие ограничения

При использовании потоков изменений действуют следующие ограничения.

* Свойства `operationType` и `updateDescription` пока не поддерживаются в выходном документе.
* В настоящее время поддерживаются типы операций `insert`, `update` и `replace`. 
* Операция удаления или другие события пока не поддерживаются.

В связи с этими ограничениями этап $match, этап $project и параметры fullDocument являются обязательными, как показано в предыдущих примерах.

В отличие от канала изменений, в API Cosmos DB для SQL нет отдельной [библиотеки обработчика канала изменений](change-feed-processor.md) для использования потоков изменений и нет необходимости в контейнере аренды. В настоящее время поддержка [триггеров Функций Azure](change-feed-functions.md) для обработки потоков изменений отсутствует.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения.

* **Код ошибки HTTP: 16500** — когда поток изменений регулируется, он возвращает пустую страницу.

* **NamespaceNotFound (OperationType Invalidate)**  — при запуске потока изменений в несуществующей коллекции или при удалении коллекции возвращается ошибка `NamespaceNotFound`. Так как свойство `operationType` не может быть возвращено в выходном документе, вместо ошибки `operationType Invalidate` возвращается ошибка `NamespaceNotFound`.

## <a name="examples"></a>Примеры

В следующем примере показано, как получить потоки изменений для всех элементов в коллекции. В этом примере создается курсор для просмотра элементов при их вставке, обновлении или замене. Для получения потоков изменений требуются этап `$match`, этап `$project` и параметр `fullDocument`. Наблюдение за операциями удаления с помощью потоков изменений сейчас не поддерживается. В качестве обходного решения в удаляемые элементы можно добавить программный маркер. Например, можно добавить атрибут в элемент с именем "deleted". Если вы захотите удалить элемент, можно присвоить аргументу "deleted" значение `true` и задать срок жизни для элемента. Так как обновление "deleted" до `true` является изменением, оно будет отображено в потоке изменений.

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

В следующем примере показано, как получить изменения элементов в пределах одного сегмента. В этом примере показано получение изменений элементов, ключ сегмента которых равен "a", а значение ключа сегмента равно "1". Допускается ситуация, когда разные клиенты могут одновременно считывать изменения из разных сегментов.

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

## <a name="current-limitations"></a>Текущие ограничения

При использовании потоков изменений действуют следующие ограничения.

* Свойства `operationType` и `updateDescription` пока не поддерживаются в выходном документе.
* В настоящее время поддерживаются типы операций `insert`, `update` и `replace`. Операция удаления или другие события пока не поддерживаются.

В связи с этими ограничениями этап $match, этап $project и параметры fullDocument являются обязательными, как показано в предыдущих примерах.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения.

* **Код ошибки HTTP: 429** — когда поток изменений регулируется, он возвращает пустую страницу.

* **NamespaceNotFound (OperationType Invalidate)**  — при запуске потока изменений в несуществующей коллекции или при удалении коллекции возвращается ошибка `NamespaceNotFound`. Так как свойство `operationType` не может быть возвращено в выходном документе, вместо ошибки `operationType Invalidate` возвращается ошибка `NamespaceNotFound`.

## <a name="next-steps"></a>Дальнейшие действия

* [Завершение срока действия данных с помощью API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Индексирование в API Azure Cosmos DB для MongoDB](mongodb-indexing.md)