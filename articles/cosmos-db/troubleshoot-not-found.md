---
title: Устранение неполадок Azure Cosmos DB не найденных исключений
description: Узнайте, как диагностировать и исправить исключения, не найденные.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1d778b4330389d23b0fe7179a005abfbd7d66d5c
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871111"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Диагностика и устранение неполадок Azure Cosmos DB не найденных исключений
Код состояния HTTP 404 означает, что ресурс больше не существует.

## <a name="expected-behavior"></a>Ожидаемое поведение
Существует множество допустимых сценариев, в которых приложение принимает код 404 и правильно обрабатывает сценарий.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Было возвращено исключение "не найдено" для элемента, который должен существовать или существовать
Ниже приведены возможные причины, по которым код состояния 404 возвращается, если элемент должен существовать или существовать.

### <a name="race-condition"></a>Состояние гонки
Существует несколько экземпляров пакета SDK, и чтение произошло до записи.

#### <a name="solution"></a>Решение:
1. Согласованность учетной записи по умолчанию для Azure Cosmos DB — согласованность сеанса. При создании или обновлении элемента ответ возвращает маркер сеанса, который может быть передан между экземплярами пакета SDK, чтобы гарантировать чтение запроса чтения из реплики с таким изменением.
1. Измените [уровень согласованности](consistency-levels-choosing.md) на [более высокий уровень](consistency-levels-tradeoffs.md).

### <a name="invalid-partition-key-and-id-combination"></a>Недопустимая комбинация ключа секции и идентификатора
Сочетание ключа и идентификатора секции не является допустимым.

#### <a name="solution"></a>Решение:
Исправьте логику приложения, которая вызывает неверное сочетание. 

### <a name="invalid-character-in-an-item-id"></a>Недопустимый символ в ИДЕНТИФИКАТОРе элемента
Элемент вставляется в Azure Cosmos DB с [недопустимым символом](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) в идентификаторе элемента.

#### <a name="solution"></a>Решение:
Измените идентификатор на другое значение, которое не содержит специальных символов. Если изменить идентификатор не удается, можно закодировать идентификатор с помощью Base64 для экранирования специальных символов.

Элементы, уже вставленные в контейнер для идентификатора, могут быть заменены значениями RID вместо ссылок на основе имен.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Очистка времени на динамическую очистку
Для элемента было задано свойство [срока жизни (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . Элемент очищен из-за истечения срока действия свойства TTL.

#### <a name="solution"></a>Решение:
Измените свойство TTL, чтобы запретить очистку элемента.

### <a name="lazy-indexing"></a>Отложенное индексирование
[Отложенное индексирование](index-policy.md#indexing-mode) не было перехвачено.

#### <a name="solution"></a>Решение:
Дождитесь завершения индексирования или измените политику индексирования.

### <a name="parent-resource-deleted"></a>Родительский ресурс удален
База данных или контейнер, в котором находится элемент, удалена.

#### <a name="solution"></a>Решение:
1. [Восстановите](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) родительский ресурс или повторно создайте ресурсы.
1. Создайте новый ресурс для замены удаленного ресурса.

## <a name="next-steps"></a>Дальнейшие шаги
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET.
* Ознакомьтесь с рекомендациями по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md).