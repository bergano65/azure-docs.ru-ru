---
title: Доступность и согласованность в Центрах событий Azure | Документация Майкрософт
description: Узнайте, как обеспечить максимальную доступность и согласованность в Центрах событий Azure с помощью секций.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 497a6e7430c4e6f8e29f903294ca94a4cb23012b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315784"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Доступность и согласованность в Центрах событий

## <a name="overview"></a>Обзор
Центры событий Azure используют [модель секционирования](event-hubs-scalability.md#partitions) для повышения доступности и распараллеливания в пределах одного концентратора событий. Например, если концентратор событий состоит из четырех секций и одна из них перемещается с одного сервера на другой в рамках операции балансировки нагрузки, то вы можете по-прежнему выполнять отправку и получение из трех оставшихся секций. Кроме того, большее число секций обеспечивает большее количество параллельных модулей чтения для обработки данных, повышая тем самым суммарную пропускную способность. Понимание основных принципов и последствий секционирования и упорядочения в распределенной системе является критически важным аспектом разработки решений.

Чтобы лучше понять компромисс между упорядочением и доступностью, ознакомьтесь с [теоремой CAP](https://en.wikipedia.org/wiki/CAP_theorem) (известной также как теорема Брюера). В ней решается вопрос выбора между согласованностью, доступностью и устойчивостью к разделению. Кроме того, в ней указано, что в секционированных по сети системах существует компромисс между согласованностью и доступностью.

В теореме Брюера согласованность и доступность определяются следующим образом.
* Устойчивость к разделению — это способность системы обработки данных продолжать обработку, даже если в одной из секций происходит сбой.
* Доступность — защищенный от сбоев узел возвращает обоснованный ответ в течение приемлемого времени (без ошибок и простоев).
* Согласованность — при операциях чтения гарантируется получение результатов самых поздних операций записи для данного клиента.

## <a name="partition-tolerance"></a>Устойчивость к разделению
В основе Центров событий лежит модель секционированных данных. Количество секций в концентраторе событий можно настроить во время установки, и это значение невозможно изменить позднее. Поскольку в Центрах событий необходимо использовать секции, вам требуется принять решение о доступности и согласованности для своего приложения.

## <a name="availability"></a>Доступность
Самый простой способ начать работу с Центрами событий — использовать поведение по умолчанию. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 или более поздней версии)](#tab/latest)
При создании нового объекта **[евенсубпродуцерклиент](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** и использовании метода **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** события автоматически распределяются между секциями в концентраторе событий. Такое поведение обеспечивает наивысший показатель времени непрерывной работы.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 или более ранняя версия)](#tab/old)
Если вы создадите объект **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** и используете метод **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** (Отправить), то события автоматически распределятся между секциями в концентраторе событий. Такое поведение обеспечивает наивысший показатель времени непрерывной работы.

---

Для вариантов использования, требующих максимального времени работы, эта модель является предпочтительной.

## <a name="consistency"></a>Согласованность
В некоторых сценариях важным может быть упорядочение событий. Например, вам может потребоваться, чтобы серверная система обрабатывала команду обновления перед командой удаления. В этом случае можно либо задать ключ секции для события, либо использовать `PartitionSender` объект (если вы используете старую библиотеку Microsoft. Azure. Messaging) для отправки событий только в определенный раздел. Такое действие гарантирует, что при считывании этих события из секции они будут считываться по порядку. Если вы используете библиотеку **Azure. Messaging. EventHubs** и дополнительные сведения см. в разделе [миграция кода из партитионсендер в евенсубпродуцерклиент для публикации событий в секцию](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

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

Применяя такую конфигурацию, учитывайте, что в случае недоступности определенной секции, в которую выполняется отправка, вы получите сообщение об ошибке. Для сравнения: если не привязывать отправку к одной секции, то служба Центров событий отправляет событие в следующую доступную секцию.

Одно из возможных решений для обеспечения упорядочения с максимальным увеличением времени бесперебойной работы — объединение событий в рамках приложения обработки событий. Самый простой способ это сделать — пометить событие пользовательским свойством SequenceNumber (Порядковый номер). Пример кода приведен ниже.

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

* [Обзор службы Центров событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
