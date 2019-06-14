---
title: Откладывание сообщений служебной шины Azure | Документация Майкрософт
description: Откладывание доставки сообщений служебной шины.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 11ea10f1deba5a21b98dea875a1b7dc94998aa00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402740"
---
# <a name="message-deferral"></a>Откладывание сообщений

Когда клиент очереди или подписки получает сообщение, которое требуется обработать, но в настоящее время это невозможно из-за особых обстоятельств в приложении, он имеет возможность "отложить" получение этого сообщения на потом. Сообщение остается в очереди или подписке, но игнорируется.

Функция откладывания создана специально для сценариев обработки рабочих процессов. Платформы рабочих процессов могут требовать обработки определенных операций в заданном порядке, для чего может потребоваться отложить обработку некоторых полученных сообщений до завершения предписанных предшествующих операций, указанных в других сообщениях.

Простой наглядный пример — это последовательность обработки, в которой уведомление об оплате от внешнего поставщика платежных услуг появляется в системе до того, как соответствующий заказ на покупку был передан из магазина в систему обработки заказов. В этом случае система обработки заказов может отложить обработку уведомления об оплате, пока не появится заказ, с которым его можно будет связать. В сценариях рандеву, где сообщения из различных источников управляют рабочим процессом, порядок выполнения в режиме реального времени на самом деле может быть правильным, но сообщения, отражающие результаты обработки, могут приходить в неправильном порядке.

В конечном счете откладывание помогает изменить порядок, в котором сообщения поступают, на порядок, в котором они могут быть обработаны, сохраняя их в надежном хранилище сообщений, для которого требуется отложить обработку сообщений.

## <a name="message-deferral-apis"></a>Интерфейсы API откладывания сообщений

Доступные API: [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) или [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) в клиенте .NET Framework, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) в клиенте .NET Standard и **mesageReceiver.defer** или **messageReceiver.deferSync** в клиенте Java. 

Отложенные сообщения остаются в основной очереди вместе с другими активными сообщениями (в отличие от недоставленных сообщений, находящихся в подочереди), но их больше невозможно получить с помощью обычных функций Receive и ReceiveAsync. Отложенные сообщения можно будет обнаружить посредством [обзора сообщений](message-browsing.md), если приложение потеряет их.

Для получения отложенного сообщения его владелец обязан запомнить значение [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber), присваиваемое при откладывании. Любой получатель, который знает последовательный номер отложенного сообщения, позже сможет получить сообщение явным образом с помощью функции `Receive(sequenceNumber)`. Для очередей, можно использовать [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), использовать подписки на разделы [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient).

Если сообщение не удается обработать, так как конкретный ресурс для его обработки временно недоступен, но обработку этого сообщения нельзя бесцеремонно приостанавливать, можно отложить это сообщение в сторону на несколько минут, запомнив значение **SequenceNumber** в [запланированном сообщении](message-sequencing.md), которое должно быть опубликовано через несколько минут, и повторно получить отложенное сообщение при получении этого запланированного сообщения. Если все операции обработчика сообщений зависят от базы данных, и она временно недоступна, то следует не откладывать, а полностью приостанавливать получение сообщений, пока эта база данных снова не будет доступна.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об обмене сообщениями через служебную шину см. в следующих статьях:

* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)
