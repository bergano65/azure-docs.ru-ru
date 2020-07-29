---
title: Устранение неполадок Azure Cosmos DB не найденных исключений
description: Как диагностировать и исправить исключение "не найдено"
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294620"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Диагностика и устранение неполадок Azure Cosmos DB не найдены
Код состояния HTTP 404 означает, что ресурс больше не существует.

## <a name="expected-behavior"></a>Ожидаемое поведение
Существует множество допустимых сценариев, в которых приложение ожидает 404, и правильно обрабатывает сценарий.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Для элемента, который должен существовать или существует, возвращено не найдено
Ниже приведены возможные причины, по которым код состояния 404 возвращается, если элемент должен или завершает работу.

### <a name="1-race-condition"></a>1. состояние гонки
Существует несколько экземпляров пакета SDK, и чтение произошло до записи.

#### <a name="solution"></a>Решение:
1. Согласованность учетной записи по умолчанию для Cosmos DB — согласованность сеанса. При создании или обновлении элемента ответ вернет маркер сеанса, который можно передать между экземплярами пакета SDK, чтобы гарантировать чтение запроса чтения из реплики с таким изменением.
2. Изменение [уровня согласованности](consistency-levels-choosing.md) до [более надежного уровня](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. недопустимое сочетание ключа секции и идентификатора
Недопустимая комбинация ключа секции и идентификатора.

#### <a name="solution"></a>Решение:
Исправьте логику приложения, которая вызывает неверное сочетание. 

### <a name="3-invalid-character-in-item-id"></a>3. недопустимый символ в ИДЕНТИФИКАТОРе элемента
Элемент вставляется в Cosmos DB с [недопустимым символом](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) в идентификаторе элемента.

#### <a name="solution"></a>Решение:
Рекомендуется, чтобы пользователи изменили идентификатор на другое значение, не содержащее специальных символов. Если изменение идентификатора не является допустимым, можно закодировать идентификатор с помощью Base64 для экранирования специальных символов.

Элементы, уже вставленные в контейнер. идентификатор может быть заменен с помощью значений RID вместо ссылок на основе имен.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. Очистка срока жизни (TTL)
Для элемента было задано свойство [срока жизни (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . Элемент очищен, так как истек срок жизни.

#### <a name="solution"></a>Решение:
Измените срок жизни, чтобы предотвратить очистку элемента.

### <a name="5-lazy-indexing"></a>5. отложенная индексация
[Отложенное индексирование](index-policy.md#indexing-mode) не было перехвачено.

#### <a name="solution"></a>Решение:
Дождитесь, пока индексирование будет переустановлено или измените политику индексирования.

### <a name="6-parent-resource-deleted"></a>6. родительский ресурс удален
База данных и (или) контейнер, в котором находится элемент, удален.

#### <a name="solution"></a>Решение:
1. [Восстановите](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) родительский ресурс или создайте ресурсы заново.
2. Создание нового ресурса для замены удаленного ресурса

## <a name="next-steps"></a>Дальнейшие шаги
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET
* Сведения о рекомендациях по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md)