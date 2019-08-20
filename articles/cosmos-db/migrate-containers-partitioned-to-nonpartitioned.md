---
title: Миграция несекционированных контейнеров Azure Cosmos в секционированные контейнеры
description: Узнайте, как перенести все существующие несекционированные контейнеры в секционированные контейнеры.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: d51c200ebff0d92b1bcdf2c8e3e0325103e214b7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615026"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Перенос несекционированных контейнеров в секционированные контейнеры

Azure Cosmos DB поддерживает создание контейнеров без ключа секции. В настоящее время можно создавать несекционированные контейнеры с помощью Azure CLI и Azure Cosmos DB пакетов SDK (.NET, Java, NodeJs), версия которых меньше или равна 2. x. Нельзя создавать несекционированные контейнеры с помощью портал Azure. Однако такие несекционированные контейнеры не являются эластичными и имеют фиксированную емкость хранилища, равную 10 ГБ, и ограничение пропускной способности 10000 единиц запросов в секунду.

Несекционированные контейнеры являются устаревшими, поэтому необходимо перенести существующие несекционированные контейнеры в секционированные контейнеры для масштабирования хранилища и пропускной способности. Azure Cosmos DB предоставляет системный механизм для переноса несекционированных контейнеров в секционированные контейнеры. В этом документе объясняется, как выполняется автоматическая миграция всех существующих несекционированных контейнеров в секционированные контейнеры. Функцию автоматической миграции можно использовать только в том случае, если вы используете версию пакетов SDK версии V3 на всех языках.

> [!NOTE] 
> Сейчас вы не можете перенести учетные записи Azure Cosmos DB MongoDB и Gremlin API, выполнив действия, описанные в этом документе. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Миграция контейнера с помощью системного ключа секции

Для поддержки миграции Azure Cosmos DB определяет определенный системой ключ раздела с именем `/_partitionkey` во всех контейнерах, у которых нет ключа секции. Определение ключа секции нельзя изменить после миграции контейнеров. Например, определение контейнера, перенесенного в секционированный контейнер, будет выглядеть следующим образом: 

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```
 
После переноса контейнера можно создать документы, заполнив `_partitionKey` свойство вместе с другими свойствами документа. `_partitionKey` Свойство представляет ключ раздела ваших документов. 

Выбор правильного ключа секции важен для оптимального использования подготовленной пропускной способности. Дополнительные сведения см. [в статье Выбор ключа секции](partitioning-overview.md) . 

> [!NOTE]
> Вы можете воспользоваться преимуществами системного ключа секции только в том случае, если вы используете версию пакетов SDK последней версии (v3) на всех языках.

В следующем примере показан пример кода для создания документа с ключом раздела, определенным системой, и чтения этого документа.

**Представление JSON документа**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey")]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234", 
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

CosmosItemResponse<DeviceInformationItem > response = 
  await migratedContainer.Items.CreateItemAsync(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
CosmosItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  ); 

```

Полный пример см. в репозитории [кода](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) GitHub для .NET. 
                      
## <a name="migrate-the-documents"></a>Перенос документов

Хотя определение контейнера дополнено свойством ключа секции, документы в контейнере не переносятся в автоматическом режиме. Это означает, что путь к свойству `/_partitionKey` ключа системного раздела не добавляется автоматически в существующие документы. Необходимо выполнить повторное `_partitionKey` секционирование существующих документов, прочитав документы, созданные без ключа секции, и перезаписать их свойством в документах. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Доступ к документам, у которых нет ключа секции

Приложения могут обращаться к существующим документам, у которых нет ключа секции, с помощью специального системного свойства с именем "Космосконтаинерсеттингс. Нонепартитионкэйвалуе". это значение для неперенесенных документов. Это свойство можно использовать во всех операциях CRUD и запросов. В следующем примере показан пример для чтения одного документа из Нонепартитионкэй. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Полный пример повторного секционирования документов см. в репозитории [примеров .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) на сайте GitHub. 

## <a name="compatibility-with-sdks"></a>Совместимость с пакетами SDK

Более старые версии Azure Cosmos DB SDK, такие как v2. x. x и v1. x. x, не поддерживают свойство ключа секции, определенное системой. Таким образом, при чтении определения контейнера из старого пакета SDK он не содержит определения ключа секции, и эти контейнеры будут работать точно так же, как и раньше. Приложения, созданные с помощью более старой версии пакетов SDK, продолжают работать с несекционированными без изменений. 

Если перенесенный контейнер используется последней версией пакета SDK или версии v3 и вы начинаете заполнять системный ключ раздела в новых документах, вы больше не сможете получать доступ к документам (чтение, обновление, удаление, запрос), таким как документы из предыдущих пакетов SDK.

## <a name="next-steps"></a>Следующие шаги

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)