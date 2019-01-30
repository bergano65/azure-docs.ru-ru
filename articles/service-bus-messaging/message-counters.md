---
title: Количество сообщений служебной шины Azure | Документация Майкрософт
description: Получение числа сообщений служебной шины Azure.
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
ms.openlocfilehash: 43689ec92fe20e61fdfd7a145d3028a3b1ee956d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848111"
---
# <a name="message-counters"></a>Счетчики сообщений

Можно получить количество сообщений в очереди и подписке с помощью интерфейсов API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Azure Resource Manager и служебной шины, доступных в пакете SDK для .NET Framework.

С помощью PowerShell это количество можно получить следующим образом.

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Сведения о количестве сообщений

Знание числа активных сообщений помогает определить, создает ли очередь невыполненную работу, которая требует больше ресурсов для обработки, чем развернуто в настоящее время. В классе [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) доступны следующие данные счетчиков.

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): число сообщений в очереди или подписке, которые находятся в активном состоянии и готовы к доставке.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): сообщения в очереди недоставленных сообщений.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): сообщения в запланированном состоянии.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): число сообщений, которые не удалось перенести в другую очередь, или раздел, который был перемещен в очередь недоставленных сообщений для передачи.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): число сообщений, ожидающих передачи в другую очередь или раздел.

Если приложению требуется масштабировать ресурсы в зависимости от длины очереди, оно должно делать это размеренно. Получение данных счетчиков сообщений является ресурсоемкой операцией в брокере обмена сообщениями, и частое ее выполнение напрямую негативно влияет на производительность сущности.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об обмене сообщениями через служебную шину см. в следующих статьях:

* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)