---
title: Изменяйте потоки в API API Azure Cosmos DB для MongoDB
description: Узнайте, как использовать aPI-доступ потоков изменений n Azure Cosmos DB для MongoDB, чтобы получить изменения, внесенные в данные.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467783"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Изменяйте потоки в API API Azure Cosmos DB для MongoDB

[Поддержка подачи изменений](change-feed.md) в API API Azure Cosmos DB для MongoDB доступна с помощью API потоков изменений. Используя API потоков изменений, приложения могут получить изменения, внесенные в коллекцию или в элементы в одном осколок. Позже вы можете предпринять дальнейшие действия на основе результатов. Изменения в элементах коллекции фиксируются в порядке их изменения времени и порядок сортировки гарантируется на клавишу осколка.

> [!NOTE]
> Чтобы использовать потоки изменений, создайте учетную запись с версией 3.6 API API Azure Cosmos DB для MongoDB или более поздней версией. Если вы запустите примеры потока изменений против `Unrecognized pipeline stage name: $changeStream` более ранней версии, вы можете увидеть ошибку. 

Ниже приводится следующий пример, как получить потоки изменений на всех элементах коллекции. Этот пример создает курсор для просмотра элементов при их вставке, обновлении или замене. Для получения потоков изменений требуется $match этап, $project этап и опция fullDocument. Просмотр операций удаления с помощью потоков изменений в настоящее время не поддерживается. В качестве обходного пути можно добавить мягкий маркер на элементах, которые удаляются. Например, можно добавить атрибут в элемент под названием "удаленный" и установить его на "истинный" и установить TTL на элемент, так что вы можете автоматически удалить его, а также отслеживать его.

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

В следующем примере показано, как получить изменения в элементах в одном осколок. Этот пример получает изменения элементов, которые имеют осколок ключа, равный "а" и осколок ключевое значение, равное "1".

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

При использовании потоков изменений применяются следующие ограничения:

* Свойства `operationType` `updateDescription` и свойства еще не подтверждены в документе вывода.
* Типы `insert`операций и `replace` операции в настоящее время поддерживаются. `update` Удаление операции или других событий еще не поддерживается.

Из-за этих ограничений, $match этап, $project этап, и fullDocument варианты необходимы, как показано в предыдущих примерах.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения:

* **КОД ошибки HTTP 429** - Когда поток изменений регулируется, он возвращает пустую страницу.

* **NamespaceNotFound (OperationType Invalidate)** - Если вы запустите поток изменений в коллекции, `NamespaceNotFound` который не существует, или если коллекция удалена, то ошибка возвращается. Поскольку `operationType` свойство не может быть возвращено в `operationType Invalidate` выводном `NamespaceNotFound` документе, а не ошибка, ошибка возвращается.

## <a name="next-steps"></a>Дальнейшие действия

* [Используйте время, чтобы автоматически прожить до истечения срока действия данных в API API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Индексирование в API Azure Cosmos DB для MongoDB](mongodb-indexing.md)
