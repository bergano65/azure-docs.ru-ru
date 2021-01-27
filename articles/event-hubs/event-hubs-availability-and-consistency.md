---
title: Доступность и согласованность в Центрах событий Azure | Документация Майкрософт
description: Узнайте, как обеспечить максимальную доступность и согласованность в Центрах событий Azure с помощью секций.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882201"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Доступность и согласованность в Центрах событий
В этой статье содержатся сведения о доступности и согласованности, поддерживаемые концентраторами событий Azure. 

## <a name="availability"></a>Доступность
Концентраторы событий Azure распределяют риск разрушительных сбоев отдельных компьютеров или даже полных стоек в кластерах, охватывающих несколько доменов сбоя в центре обработки данных. Он реализует прозрачное обнаружение сбоев и механизмы отработки отказа таким способом, чтобы служба продолжала работать в пределах проверенных уровней обслуживания и обычно без заметного перерыва при возникновении таких сбоев. Если пространство имен концентраторов событий было создано с параметром включено для [зон доступности](../availability-zones/az-overview.md), то риск сбоя далее распределяется по трем физически разделенным средствам, а служба имеет достаточно резервных копий, чтобы быстро справиться с полной, катастрофической потерей всей ситуации. Дополнительные сведения см. в статье Служба [концентраторов событий Azure: географическое аварийное восстановление](event-hubs-geo-dr.md).

Когда клиентское приложение отправляет события в концентратор событий, события автоматически распределяются между секциями в концентраторе событий. Если Секция по какой-либо причине недоступна, события распределяются между остальными секциями. Такое поведение обеспечивает наивысший показатель времени непрерывной работы. Для вариантов использования, требующих максимального времени, рекомендуется использовать эту модель вместо отправки событий в определенную секцию. Дополнительные сведения см. в разделе [Секция](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Согласованность
В некоторых сценариях важным может быть упорядочение событий. Например, вам может потребоваться, чтобы серверная система обрабатывала команду обновления перед командой удаления. В этом сценарии клиентское приложение отправляет события в определенную секцию, чтобы их порядок сохранялся. Когда приложение-потребитель использует эти события из раздела, они считываются по порядку. 

Применяя такую конфигурацию, учитывайте, что в случае недоступности определенной секции, в которую выполняется отправка, вы получите сообщение об ошибке. В качестве точки сравнения, если у вас нет сходства с одной секцией, служба концентраторов событий отправляет событие в следующий доступный раздел.

Одно из возможных решений для обеспечения упорядочения с максимальным увеличением времени бесперебойной работы — объединение событий в рамках приложения обработки событий. Самый простой способ сделать это — пометить событие свойством настраиваемого порядкового номера.

В этом сценарии клиент-производитель отправляет события в одну из доступных секций в концентраторе событий и устанавливает соответствующий порядковый номер из приложения. Для этого решения требуется, чтобы обрабатывающее приложение сохраняло состояние, но отправителям предоставлялась конечная точка, которая скорее всего будет доступна.

## <a name="appendix"></a>Приложение

### <a name="net-examples"></a>Примеры .NET

#### <a name="send-events-to-a-specific-partition"></a>Отправка событий в определенную секцию
Либо задайте ключ секции для события, либо используйте `PartitionSender` объект (если вы используете старую библиотеку Microsoft. Azure. Messaging), чтобы отправить события только в определенный раздел. Такое действие гарантирует, что при считывании этих события из секции они будут считываться по порядку. 

Если вы используете новую библиотеку **Azure. Messaging. EventHubs** , см. статью [миграция кода из партитионсендер в евенсубпродуцерклиент для публикации событий в секцию](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 или более поздней версии)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 или более ранняя версия)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Установка порядкового номера
В следующем примере событие отменяется свойством настраиваемого порядкового номера. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 или более поздней версии)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 или более ранняя версия)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

В этом примере событие отправляется в одну из доступных секций в концентраторе событий, и ему задается соответствующий порядковый номер из приложения. Для этого решения требуется, чтобы обрабатывающее приложение сохраняло состояние, но отправителям предоставлялась конечная точка, которая скорее всего будет доступна.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Центрах событий см. в следующих источниках:

* [Обзор службы Центров событий](./event-hubs-about.md)
* [Создание концентратора событий](event-hubs-create.md)
