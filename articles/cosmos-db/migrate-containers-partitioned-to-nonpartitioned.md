---
title: Мигрировать неразделенные контейнеры Azure Cosmos в разделитеные контейнеры
description: Узнайте, как перенести все существующие неразделенные контейнеры в раздельные контейнеры.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623348"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Мигрировать неразделенные контейнеры в разделенные контейнеры

Azure Cosmos DB поддерживает создание контейнеров без ключа раздела. В настоящее время можно создавать неразделенные контейнеры с помощью Azure CLI и Azure Cosmos DDKs (.Net, Java, NodeJs), которые имеют версию меньше или равна 2.x. Невозможно создавать неразделенные контейнеры с помощью портала Azure. Однако такие неразделенные контейнеры не являются эластичными и имеют фиксированную емкость хранения 20 ГБ и предел пропускной способности 10K RU/s.

Неразделенные контейнеры устаревают, и вы должны перенести существующие неразделенные контейнеры в разделенные контейнеры для масштабирования хранилища и пропускной их пропускной всей входной. Azure Cosmos DB предоставляет системный механизм для переноса контейнеров, не разделенных, в разделитеные контейнеры. В этом документе объясняется, как все существующие неразделенные контейнеры автоматически переносятся в разделинные контейнеры. Вы можете воспользоваться функцией автоматической миграции только в том случае, если вы используете версию SDK V3 на всех языках.

> [!NOTE]
> В настоящее время нельзя мигрировать учетные записи Azure Cosmos DB MongoDB и API Gremlin, используя шаги, описанные в этом документе.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Контейнер мигрировать с помощью системного ключа раздела

Для поддержки миграции Azure Cosmos DB предоставляет системный ключ раздела, указанный `/_partitionkey` на всех контейнерах, не имеющем ключа раздела. Невозможно изменить определение ключа раздела после переноса контейнеров. Например, определение контейнера, который переносится в разделинный контейнер, будет следующим:

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

После переноса контейнера можно создать документы, `_partitionKey` заселив свойство вместе с другими свойствами документа. Свойство `_partitionKey` представляет собой ключ раздела ваших документов.

Выбор правильного ключа раздела имеет важное значение для оптимального использования прокладки. Для получения дополнительной информации см., [как выбрать раздел ключевой статьи.](partitioning-overview.md)

> [!NOTE]
> Вы можете воспользоваться системным ключом раздела только в том случае, если вы используете последнюю версию SDK/V3 на всех языках.

В следующем примере показан пример кода для создания документа с системным ключом раздела и чтения этого документа:

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

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Полный пример см. [.Net samples][1]
                      
## <a name="migrate-the-documents"></a>Перенести документы

В то время как определение контейнера усиливается свойством ключа раздела, документы внутри контейнера не являются автоматически минимированными. Это означает, что `/_partitionKey` путь ключа свойства ключа раздела системы не добавляется автоматически в существующие документы. Необходимо переодеть существующие документы, прочитав документы, созданные без ключа раздела, и переписать их обратно с `_partitionKey` свойством в документах.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Доступ к документам, не имеющие ключа раздела

Приложения могут получить доступ к существующим документам, которые не имеют ключа раздела, используя свойство специальной системы под названием "PartitionKey.None", это значение не-миграционных документов. Это свойство можно использовать во всех операциях CRUD и запросов. В следующем примере показан пример для прочтения одного документа из NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Полный пример о том, как переоставить [.Net samples][1] документы на повторение, см. 

## <a name="compatibility-with-sdks"></a>Совместимость с SDK

Старая версия SDK Azure Cosmos DB, таких как V2.x.x и V1.x.x, не поддерживает свойство ключа раздела, определяемого системой. Таким образом, когда вы читаете определение контейнера из старого SDK, оно не содержит какого-либо определения ключа раздела, и эти контейнеры будут вести себя точно так же, как раньше. Приложения, построенные со старой версией SDK, продолжают работать с неразделимыми, как это происходит без каких-либо изменений. 

Если миграционный контейнер потребляется последней версией SDK/V3 и вы начинаете заполнять ключ раздела системы в новых документах, вы больше не можете получить доступ (читать, обновлять, удалять, зажимать) таким документам из старых SDK.

## <a name="known-issues"></a>Известные проблемы

**Запрос количества элементов, которые были вставлены без ключа раздела с помощью V3 SDK, может включать более высокое потребление пропускной воды**

Если вы запросите от V3 SDK элементы, которые вставляются с помощью V2 SDK, `PartitionKey.None` или элементы, вставленные с помощью `PartitionKey.None` V3 SDK с параметром, запрос подсчета может потреблять больше RU/s, если параметр поставляется в FeedOptions. Мы рекомендуем не поставлять `PartitionKey.None` параметр, если нет других элементов, вставленных ключом раздела.

Если новые элементы вставляются с различными значениями для ключа раздела, запрос `FeedOptions` на такой элемент рассчитывает путем передачи соответствующего ключа в не будет иметь никаких проблем. После ввода новых документов с ключом раздела, если вам нужно задать запрос только количество документов без значения ключа раздела, этот запрос может снова понести более высокие RU/s, аналогичные обычным разделенным коллекциям.

## <a name="next-steps"></a>Дальнейшие действия

* [Секционирование в базе данных Azure Cosmos DB](partitioning-overview.md)
* [Запросные подразделения в Azure Cosmos DB](request-units.md)
* [Обеспечение необходимой пропускной способности для контейнеров и баз данных](set-throughput.md)
* [Использование учетной записи Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration