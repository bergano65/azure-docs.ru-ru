---
title: Общие сведения об обработке транзакций в служебной шине Azure
description: В этой статье приводятся общие сведения об обработке транзакций и функции отправки через служебную шину Azure.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f51e570775fbce8a316d98b5198fa906173dc755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999960"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Обзор обработки транзакций в служебной шине

Эта статья описывает возможности служебной шины Microsoft Azure по работе с транзакциями. Большая часть обсуждения иллюстрируется [AMQP транзакциями с помощью служебной шины](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Эта статья рассматривает лишь обработку транзакций и функцию *отправить через* в служебной шине, но пример атомарных транзакций образец гораздо масштабнее и сложнее.

## <a name="transactions-in-service-bus"></a>Транзакции в служебной шине

*Транзакция* группирует две или более операций в *область выполнения*. По своей природе такая транзакция должна обеспечивать либо успешное, либо неудачное выполнение всех относящихся к данной группе операций. В этом отношении транзакция выступает в качестве единого целого, что часто называется *атомарностью*.

Служебная шина является брокером для транзакционных сообщений и гарантирует целостность всех внутренних операций в соответствии с хранилищами сообщений. Все передачи сообщений внутри служебной шины, например перемещение сообщений в [очередь недоставленных сообщений](service-bus-dead-letter-queues.md) или [автоматическая пересылка](service-bus-auto-forwarding.md) сообщений между сущностями, являются транзакционными. Таким образом, когда служебная шина принимает сообщение, оно уже сохранено и помечено порядковым номером. Начиная с этого момента любые передачи сообщений в служебной шине, являются скоординированными операциями между сущностями и не приведут ни к потере (успех исходной части, сбой целевой части), ни к дублированию (сбой исходной части, успех целевой части) сообщения.

Служебная шина поддерживает операции группирования относительно одной сущности обмена сообщениями (очереди, раздела, подписки) в области транзакции. Например, можно отправить несколько сообщений в одну очередь из области транзакций, и сообщение будет зафиксировано в журнале очереди только после успешного завершения транзакции.

## <a name="operations-within-a-transaction-scope"></a>Операции в области транзакций

Далее приведены операции, которые могут выполняться в области транзакций.

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: `Complete` , `CompleteAsync` , `Abandon` , `AbandonAsync` , `Deadletter` , `DeadletterAsync` , `Defer` , `DeferAsync` , `RenewLock` , `RenewLockAsync` 

Операции получения не указаны, так как предполагается, что приложение получает сообщения с помощью режима [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode), внутри некоторого цикла получения или с помощью с обратного вызова [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) и только затем открывает область транзакций для обработки сообщения.

Обработка сообщения (выполнено, прервано, отложено, не доставлено) происходит в пределах области и зависит от общего результата транзакции.

## <a name="transfers-and-send-via"></a>Передачи и функция "отправить через"

Чтобы обеспечить транзакционное перемещение в данных из очереди в обработчик, а затем в другую очередь, служебная шина поддерживает *передачи*. При выполнении операции передачи отправитель сначала отправляет сообщение в *очередь передачи*, а очередь передачи немедленно перемещает сообщение в предполагаемую очередь назначения, используя ту же надежную реализацию передачи, которую использует функция перемотки. Сообщение никогда не фиксируется в журнале очереди передачи так, чтобы стать видимым для получателей очереди передачи.

Эффективность этой возможности становится очевидной, когда сама очередь передачи является источником для входных сообщений отправителя. Другими словами, служебная шина может передавать сообщения в очередь назначения "через" очередь передачи, при этом делая входное сообщение завершенным (либо отложенным или недоставленным) в рамках одной атомарной операции. 

### <a name="see-it-in-code"></a>Пример кода

Для настройки таких передач вы создаете отправитель сообщений, сориентированный на очередь назначения через очередь передачи. Также есть получатель, который извлекает сообщения из той же очереди. Пример:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Затем простая транзакция использует эти элементы, как показано в следующем примере. Чтобы ознакомиться с полным примером, обратитесь к [исходному коду на сайте GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Время ожидания
Время ожидания транзакции истекло через 2 минуты. Таймер транзакции запускается, когда начинается первая операция в транзакции. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об очередях служебной шины см. в следующих статьях:

* [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Объединение в цепочки сущностей служебной шины с помощью автоматической переадресации](service-bus-auto-forwarding.md)
* [Пример с пересылкой](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Пример выполнения атомарных транзакций с помощью служебной шины](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Сравнение службы очередей Azure и службы очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


