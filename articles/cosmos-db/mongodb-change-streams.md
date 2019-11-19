---
title: Изменение потоков в API-интерфейсе Azure Cosmos DB для MongoDB
description: Узнайте, как использовать API-интерфейс Azure Cosmos DB изменений n для MongoDB, чтобы получить изменения, внесенные в данные.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: b32eb7a7236871a06e1fbed5b9f1aac91675c6f3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174737"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Изменение потоков в API-интерфейсе Azure Cosmos DB для MongoDB

Поддержка [веб-канала изменений](change-feed.md) в API Azure Cosmos DB для MongoDB доступна с помощью API потоков изменений. С помощью API изменений потоков приложения могут получать изменения, внесенные в коллекцию или элементы одного сегмента. Позже можно будет выполнить дальнейшие действия на основе результатов. Изменения элементов в коллекции фиксируются в порядке их изменения, и порядок сортировки гарантирован на каждом ключе сегмента.

В следующем примере показано, как получить потоки изменений для всех элементов в коллекции. В этом примере создается курсор для просмотра элементов при их вставке, обновлении или замене. Для получения потоков изменений требуется этап $match, $project Stage и параметр Фуллдокумент. Наблюдение за операциями удаления с помощью потоков изменений сейчас не поддерживается. В качестве обходного решения можно добавить мягкий маркер для удаляемых элементов. Например, можно добавить атрибут в элемент с именем "Deleted" и задать для него значение "true" и задать TTL для элемента, чтобы можно было автоматически удалить его и отслеживанить.

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

В следующем примере показано, как получить изменения элементов в одном сегменте, например сегмент "a", имеющий элементы со значением "1".

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

При использовании потоков изменений применяются следующие ограничения.

* Свойства `operationType` и `updateDescription` пока не поддерживаются в выходном документе.
* Типы операций `insert`, `update`и `replace` в настоящее время поддерживаются. Операция удаления или другие события пока не поддерживаются.

Из-за этих ограничений требуется $match этап, $project этап и параметры Фуллдокумент, как показано в предыдущих примерах.

## <a name="error-handling"></a>Обработка ошибок

При использовании потоков изменений поддерживаются следующие коды ошибок и сообщения:

* **Код ошибки HTTP 429** — когда поток изменений регулируется, возвращается пустая страница.

* **Намеспаценотфаунд (OperationType unvalidate)** — при запуске потока изменений в несуществующей коллекции или при удалении коллекции возвращается ошибка `NamespaceNotFound`. Поскольку свойство `operationType` не может быть возвращено в выходном документе вместо ошибки `operationType Invalidate`, возвращается ошибка `NamespaceNotFound`.

## <a name="next-steps"></a>Дополнительная информация

* [Используйте срок жизни для автоматического истечения срока действия данных в API Azure Cosmos DB для MongoDB](mongodb-time-to-live.md)
* [Индексирование в API Azure Cosmos DB для MongoDB](mongodb-indexing.md)