---
title: Очереди недоставленных сообщений служебной шины | Документация Майкрософт
description: Описание очередей недоставленных сообщений в служебной шине Azure. Очереди служебной шины и подписки на разделы предоставляют вторичную подочередь, которая называется очередью недоставленных сообщений.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158913"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Обзор очередей недоставленных сообщений служебной шины

Очереди и подписки служебной шины Azure предоставляют вторичную подочередь, которая называется *очередью недоставленных сообщений* (очередь DLQ). Очередь недоставленных сообщений не требует явного создания и не может быть удалена или иным образом управляется независимо от основной сущности.

В этой статье рассматриваются очереди недоставленных сообщений в служебной шине. Большая часть дискуссии проиллюстрирована с помощью [примера «очереди недоставленных сообщений](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) » на GitHub.
 
## <a name="the-dead-letter-queue"></a>Очередь недоставленных сообщений

Очередь недоставленных сообщений заключается в том, чтобы хранить сообщения, которые не могут быть доставлены какому-либо получателю, или сообщения, которые не удалось обработать. Сообщение из очереди DLQ можно извлечь и изучить. Приложение с помощью оператора может исправить проблемы и повторно отправить это сообщение, зарегистрировать ошибку и предпринять действие по исправлению. 

С точки зрения интерфейса API и протокола очередь DLQ очень похожа на другие очереди за исключением того, что сообщения могут отправляться только посредством операции недоставленного сообщения родительской сущности. Кроме того, срок жизни не наблюдается, и вы не можете недоставлено сообщение от очередь DLQ. Очередь недоставленных сообщений полностью поддерживает доставку с блокировкой для просмотра и транзакционные операции.

Автоматическая очистка очередь DLQ отсутствует. Сообщения остаются в очереди DLQ, пока вы явно не извлечете их оттуда и не вызовете метод [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) для недоставленного сообщения.

## <a name="dlq-message-count"></a>Число сообщений очередь DLQ
Невозможно получить количество сообщений в очереди недоставленных писем на уровне разделов. Это обусловлено тем, что сообщения не находятся на уровне разделов, если служебная шина не выдает внутреннюю ошибку. Вместо этого, когда отправитель отправляет сообщение в раздел, сообщение пересылается в подписку для раздела в течение миллисекунд и, следовательно, больше не находится на уровне раздела. Таким образом, вы можете просмотреть сообщения в очередь DLQ, связанном с подпиской для раздела. В следующем примере **Обозреватель служебной шины** показывает, что в очередь DLQ для подписки "test1" находится 62 сообщений. 

![Число сообщений очередь DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Количество сообщений очередь DLQ можно также получить с помощью команды Azure CLI: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show). 

## <a name="moving-messages-to-the-dlq"></a>Перемещение сообщений в очередь DLQ

В служебной шине есть несколько действий, которые приводят к отправке сообщений в очередь DLQ из самой системы обработки сообщений. Сообщения также могут явно перемещаться в очередь DLQ самим приложением. 

Когда брокер перемещает сообщение, к нему добавляется два свойства в момент вызова брокером своей внутренней версии метода [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) для сообщения: `DeadLetterReason` и `DeadLetterErrorDescription`.

Приложения могут определять свои собственные коды для свойства `DeadLetterReason`, однако система задает следующие значения.

| Условие | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Всегда |HeaderSizeExceeded |Превышен максимальный размер для этого потока. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Время жизни сообщения истекло, и оно было перемещено в очередь недоставленных. |
| SubscriptionDescription.RequiresSession |Идентификатор сеанса имеет значение null. |Сущность, поддерживающая сеансы, не допускает использование сообщений, идентификатор сеанса которых имеет значение null. |
| !очередь недоставленных сообщений | MaxTransferHopCountExceeded | Максимальное количество допустимых прыжков при пересылке между очередями. Значение равно 4. |
| Явное перемещение приложением в очередь недоставленных сообщений |Задается приложением |Задается приложением |

## <a name="exceeding-maxdeliverycount"></a>Превышено значение MaxDeliveryCount

Очереди и подписки имеют свойства [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) и [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) соответственно. Значение по умолчанию — 10. Всякий раз, когда сообщение доставляется с блокировкой ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), но затем от него явно отказываются или срок действия блокировки истекает, значение счетчика [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) увеличивается. Когда значение [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) превышает [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), сообщение перемещается в очередь DLQ с указанием кода причины `MaxDeliveryCountExceeded`.

Это поведение нельзя отключить, но можно задать для [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) большое число.

## <a name="exceeding-timetolive"></a>Превышение значения TimeToLive

Когда свойство [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) или [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) имеет значение **true** (по умолчанию — **false**), все просроченные сообщения перемещаются в очередь DLQ с указанием кода причины `TTLExpiredException`.

Сообщения с истекшим сроком действия удаляются и перемещаются в очередь DLQ, если имеется хотя бы один активный получатель, исходящие из основной очереди или подписки. Это поведение характерно для разработки.

## <a name="errors-while-processing-subscription-rules"></a>Ошибки при обработке правил подписки

Когда для подписки активировано свойство [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription), любые ошибки, возникающие при исполнении правила SQL-фильтрации подписки, фиксируются в очереди DLQ вместе с сообщением, вызвавшим ошибку.

## <a name="application-level-dead-lettering"></a>Недоставленные сообщения на уровне приложения

Помимо системных функций работы с недоставленными сообщениями, приложения могут использовать очередь DLQ для явного отклонения недопустимых сообщений. Они могут включать сообщения, которые не могут быть правильно обработаны из-за проблем с системой, сообщений, которые содержат неверно сформированные полезные данные, или сообщения, которые не проходят проверку подлинности при использовании какой-либо схемы безопасности на уровне сообщений.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Недоставка сообщений в сценариях ForwardTo или SendVia

Сообщения отправляются в очередь недоставленных сообщений при следующих условиях:

- Сообщение проходит через более четырех очередей или разделов, Объединенных в [цепочку](service-bus-auto-forwarding.md).
- Целевая очередь (или раздел) отключена или удалена.
- Очередь или раздел назначения превышает максимальный размер сущности.

Для получения этих недоставленных сообщений можно создать получатель с помощью вспомогательного метода [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath).

## <a name="example"></a>Пример

Следующий фрагмент кода создает получатель сообщений. В цикле получения для основной очереди код получает сообщение с помощью метода [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), который запрашивает у брокера мгновенный возврат любых доступных сообщений или возврат без результата. Если код получает сообщение, он немедленно отказывается от него, что ведет к увеличению `DeliveryCount`. Когда система перемещает сообщение в очередь DLQ, основная очередь оказывается пуста и осуществляется выход из цикла, так как [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) возвращает значение **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Путь к очереди недоставленных сообщений
Доступ к очереди недоставленных сообщений можно получить с помощью следующего синтаксиса:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Если вы используете пакет SDK для .NET, можно получить путь к очереди недоставленных сообщений с помощью метода SubscriptionClient. FormatDeadLetterPath (). Этот метод принимает имя раздела, имя подписки и суффиксы с **/$DeadLetterQueue**.


## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об очередях служебной шины см. в следующих статьях:

* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Сравнение службы очередей Azure и службы очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

