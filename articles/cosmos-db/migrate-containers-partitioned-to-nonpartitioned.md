---
title: Перенести контейнеры Azure Cosmos DB без секционирования для секционированных контейнеров
description: Узнайте, как перенести все существующие несекционированной контейнеры в секционированных контейнеров.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e8aaee8cb7df794652b2c560c5250921c1a4e060
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160640"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Перенос контейнеров без секционирования для секционированных контейнеров

Azure Cosmos DB поддерживает создание контейнеров без ключа секции. В настоящее время можно создать без секционирования контейнеры с помощью Azure CLI и Azure Cosmos DB пакетов SDK (.Net, Java, NodeJs), версия меньше или равно 2.x. Невозможно создать без секционирования контейнеры с помощью портала Azure. Тем не менее такие контейнеры без секционирования не эластичной и устранили емкость хранилища 10 ГБ и пропускной способности ограничение в 10 ЕЗ/с.

Контейнеры несекционированной прежних версий и необходимо перенести существующие контейнеры без секционирования для секционированных контейнеров, чтобы масштабировать хранилище и пропускную способность. Azure Cosmos DB предоставляет механизм определения системы переноса контейнеров без секционирования для секционированных контейнеров. В этом документе объясняется, как все существующие несекционированной контейнеры — это автоматически перенесенных в секционированных контейнеров. Можно воспользоваться преимуществами функции автоматического переноса, только в том случае, если вы используете версию V3 SDK на всех языках.

> [!NOTE] 
> В настоящее время нельзя перенести учетные записи Azure Cosmos DB MongoDB и Gremlin API с помощью действия, описанные в этом документе. 

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Перенос контейнера с помощью ключа секции определенные системой

Чтобы обеспечить поддержку миграции, Azure Cosmos DB определяется системой секции ключ с именем `/_partitionkey` во всех контейнерах, у которых нет ключа секции. После переноса контейнеры нельзя изменить определение ключа секции. Например определение контейнера, который переносится в секционированный контейнер будет следующим: 

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
 
После миграции контейнера можно создать документы, заполнив `_partitionKey` свойство вместе с другими свойствами документа. `_partitionKey` Свойство представляет ключ секции документов. 

Выбор подходящего ключа секции важен для оптимального использования подготовленной пропускной способности. Дополнительные сведения см. в разделе [по выбору ключа секции](partitioning-overview.md) статьи. 

> [!NOTE]
> Только в том случае, если вы используете последнюю версию/V3 версию SDK на всех языках, можно воспользоваться преимуществами системный ключ определенной секции.

В следующем примере показано пример кода для создания документа с ключом секции, определенные системы и читать этот документ:

**JSON-представление документа**

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

Полный пример см. в разделе [примеры .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) репозитория GitHub. 
                      
## <a name="migrate-the-documents"></a>Перенос документов

Во время определения контейнера дополнено свойство ключа секции, документам в контейнере не автоматического переноса. То есть свойство ключа секции системы `/_partitionKey` путь не добавляется автоматически к существующих документов. Необходимо заново секционировать существующих документов путем чтения документов, которые были созданы без ключа секции и переписывать их обратно с помощью `_partitionKey` свойства в документах. 

## <a name="access-documents-that-dont-have-a-partition-key"></a>Доступ к документам, у которых нет ключа секции

Приложения могут обращаться к существующих документов, у которых нет ключа секции, с помощью специальных системных свойства, названного «CosmosContainerSettings.NonePartitionKeyValue», это значение не перенесенные документов. Это свойство можно использовать во всех операциях CRUD и запросов. Приведенный ниже образец для чтения из NonePartitionKey одного документа. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: CosmosContainerSettings.NonePartitionKeyValue, 
  id: device.Id
); 

```

Полный пример на повторное разбиение в документах, см. в разделе [примеры .net](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples) репозитория GitHub. 

## <a name="compatibility-with-sdks"></a>Совместимость с пакетами SDK

Пакеты SDK Azure Cosmos DB, например V2.x.x и V1.x.x более старой версии не поддерживают системные свойства ключа секции. Таким образом при считывании определения контейнера из старые версии SDK, она не содержит любое определение ключа секции, и эти контейнеры будут вести себя точно как раньше. Приложения, построенные с помощью более старой версии пакетов SDK продолжить работу с несекционированной как есть без изменений. 

Если перенесенных контейнеров, используемое последнюю версию/V3 версию пакета SDK и начать заполнение ключа секции системы и определены в рамках новых документов, не может получить доступ к (чтение, обновление, удаление и запрос) такие документы от более старые пакеты SDK больше.

## <a name="next-steps"></a>Дальнейшие действия

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Единицы запросов в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)