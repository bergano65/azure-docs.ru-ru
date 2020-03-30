---
title: Очереди недоставленных сообщений служебной шины | Документация Майкрософт
description: Описывает очереди в автобусе Azure Service. Очереди сервисных автобусов и тематические подписки обеспечивают второстепенную очередь, называемую очередью с мертвымписьмом.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158913"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Обзор очередей недоставленных сообщений служебной шины

Очереди Azure Service Bus и тематические подписки предоставляют второстепенную очередь, называемую *очередью с мертвымписьмом* (DL). Очередь с мертвым письмом не должна быть явно создана и не может быть удалена или иным образом управляется независимо от основной сущности.

В этой статье рассматриваются очереди недоставленных сообщений в служебной шине. Большая часть обсуждения иллюстрируется [образцом очередей Dead-Letter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) на GitHub.
 
## <a name="the-dead-letter-queue"></a>Очередь недоставленных сообщений

Цель очереди с мертвым письмом состоит в том, чтобы хранить сообщения, которые не могут быть доставлены в любой приемник, или сообщения, которые не могут быть обработаны. Сообщение из очереди DLQ можно извлечь и изучить. Приложение с помощью оператора может исправить проблемы и повторно отправить это сообщение, зарегистрировать ошибку и предпринять действие по исправлению. 

С точки зрения интерфейса API и протокола очередь DLQ очень похожа на другие очереди за исключением того, что сообщения могут отправляться только посредством операции недоставленного сообщения родительской сущности. Кроме того, время в прямом эфире не наблюдается, и вы не можете мертвого письма сообщение от DL. Очередь недоставленных сообщений полностью поддерживает доставку с блокировкой для просмотра и транзакционные операции.

Автоматической очистки ДЛЗ не существует. Сообщения остаются в очереди DLQ, пока вы явно не извлечете их оттуда и не вызовете метод [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) для недоставленного сообщения.

## <a name="dlq-message-count"></a>Количество сообщений DL
Невозможно получить количество сообщений в очереди мертвых букв на уровне темы. Это потому, что сообщения не сидят на уровне темы, если Служба Автобус бросает внутреннюю ошибку. Вместо этого, когда отправитель отправляет сообщение на тему, сообщение направляется в подписки на тему в течение миллисекунд и, таким образом, больше не находится на уровне темы. Таким образом, вы можете видеть сообщения в DL, связанные с подпиской на эту тему. В следующем **примере, Service Bus Explorer** показывает, что в настоящее время в ДЛЗ имеется 62 сообщения для подписки "test1". 

![Количество сообщений DL](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Вы также можете получить количество сообщений DL, используя [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)команду Azure CLI: . 

## <a name="moving-messages-to-the-dlq"></a>Перемещение сообщений в очередь DLQ

В служебной шине есть несколько действий, которые приводят к отправке сообщений в очередь DLQ из самой системы обработки сообщений. Сообщения также могут явно перемещаться в очередь DLQ самим приложением. 

Когда брокер перемещает сообщение, к нему добавляется два свойства в момент вызова брокером своей внутренней версии метода [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) для сообщения: `DeadLetterReason` и `DeadLetterErrorDescription`.

Приложения могут определять свои собственные коды для свойства `DeadLetterReason`, однако система задает следующие значения.

| Условие | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Всегда |HeaderSizeExceeded |Превышен максимальный размер для этого потока. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Время жизни сообщения истекло, и оно было перемещено в очередь недоставленных. |
| SubscriptionDescription.RequiresSession |Идентификатор сеанса недействителен. |Сущность, поддерживающая сеансы, не допускает использование сообщений, идентификатор сеанса которых имеет значение null. |
| !очередь недоставленных сообщений | MaxTransferHopCountExceeded | Максимальное количество разрешенных переходов при пересылке между очередями. Значение устанавливается до 4. |
| Явное перемещение приложением в очередь недоставленных сообщений |Задается приложением |Задается приложением |

## <a name="exceeding-maxdeliverycount"></a>Превышено значение MaxDeliveryCount

Очереди и подписки имеют свойства [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) и [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) соответственно. Значение по умолчанию — 10. Всякий раз, когда сообщение доставляется с блокировкой ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), но затем от него явно отказываются или срок действия блокировки истекает, значение счетчика [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) увеличивается. Когда значение [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) превышает [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), сообщение перемещается в очередь DLQ с указанием кода причины `MaxDeliveryCountExceeded`.

Это поведение не может быть отключено, но вы можете настроить [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) на большое количество.

## <a name="exceeding-timetolive"></a>Превышение значения TimeToLive

Когда свойство [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) или [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) имеет значение **true** (по умолчанию — **false**), все просроченные сообщения перемещаются в очередь DLQ с указанием кода причины `TTLExpiredException`.

Просроченные сообщения только продуваются и перемещаются в DL, когда есть по крайней мере один активный приемник, вытягивая из основной очереди или подписки; это поведение по замыслу.

## <a name="errors-while-processing-subscription-rules"></a>Ошибки при обработке правил подписки

Когда для подписки активировано свойство [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription), любые ошибки, возникающие при исполнении правила SQL-фильтрации подписки, фиксируются в очереди DLQ вместе с сообщением, вызвавшим ошибку.

## <a name="application-level-dead-lettering"></a>Недоставленные сообщения на уровне приложения

Помимо системных функций работы с недоставленными сообщениями, приложения могут использовать очередь DLQ для явного отклонения недопустимых сообщений. Они могут включать сообщения, которые не могут быть должным образом обработаны из-за какой-либо системной проблемы, сообщения, которые держат неправильно установленные полезные нагрузки, или сообщения, которые не аутентифицируются при использовании какой-либо схемы безопасности на уровне сообщений.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Недоставка сообщений в сценариях ForwardTo или SendVia

Сообщения отправляются в очередь недоставленных сообщений при следующих условиях:

- Сообщение проходит через более чем четыре очереди или темы, которые [прикованы друг к другу.](service-bus-auto-forwarding.md)
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

## <a name="path-to-the-dead-letter-queue"></a>Путь к очереди мертвых букв
Вы можете получить доступ к очереди мертвых букв, используя следующий синтаксис:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Если вы используете SDK .NET, вы можете получить путь к очереди мертвых букв с помощью метода SubscriptionClient.FormatDeadLetterPath() . Этот метод принимает имя темы /подписка имя и suffixes с **/$DeadLetterQueue**.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об очередях служебной шины см. в следующих статьях:

* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Сравнение службы очередей Azure и службы очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

