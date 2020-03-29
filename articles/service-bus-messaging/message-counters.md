---
title: Автобус службы Azure - количество сообщений
description: Изысайте количество сообщений, хранящегося в очередях и подписках, с помощью диспетчера ресурсов Azure и AIS Azure Service Bus NamespaceManager.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3a4fca0b3b60fcb76bcdc4f5f2d53df816c5053b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756388"
---
# <a name="message-counters"></a>Счетчики сообщений

Можно получить количество сообщений в очереди и подписке с помощью интерфейсов API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Azure Resource Manager и служебной шины, доступных в пакете SDK для .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

С помощью PowerShell это количество можно получить следующим образом.

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Сведения о количестве сообщений

Знание числа активных сообщений помогает определить, создает ли очередь невыполненную работу, которая требует больше ресурсов для обработки, чем развернуто в настоящее время. В классе [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) доступны следующие данные счетчиков.

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): число сообщений в очереди или подписке, которые находятся в активном состоянии и готовы к доставке.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): число сообщений в очереди недоставленных сообщений.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): число запланированных сообщений.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): число сообщений, которые не удалось перенести в другую очередь или раздел и которые были перемещены в очередь недоставленных сообщений для передачи.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): число сообщений, ожидающих передачи в другую очередь или раздел.

Если приложению требуется масштабировать ресурсы в зависимости от длины очереди, оно должно делать это размеренно. Получение данных счетчиков сообщений является ресурсоемкой операцией в брокере обмена сообщениями, и частое ее выполнение напрямую негативно влияет на производительность сущности.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об обмене сообщениями через служебную шину см. в следующих статьях:

* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)
