---
title: Ошибки в ошибках Am-P в автобусе службы Azure Документы Майкрософт
description: Предоставляет список ошибок АМЗП, которые вы можете получить при использовании Azure Service Bus, и причина этих ошибок.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402791"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Ошибки АМЗП в сервисном автобусе Azure
В этой статье приводятся некоторые ошибки, которые вы получаете при использовании AM-P с Azure Service Bus. Все они являются стандартным поведением службы. Вы можете избежать их, сделав отправку/ получение вызовов на соединение/ссылку, которая автоматически воссоздает соединение/ссылку.

## <a name="link-is-closed"></a>Ссылка закрыта 
Вы видите следующую ошибку, когда соединение и ссылка АМЗП активны, но никакие звонки (например, отправляются или получают) по ссылке в течение 10 минут. Итак, ссылка закрыта. Соединение по-прежнему открыто.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Соединение закрыто
Вы видите следующую ошибку в подключении AM-P, когда все ссылки в соединении были закрыты, потому что не было активности (простоя) и новая ссылка не была создана в течение 5 минут.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Ссылка не создается 
Вы видите эту ошибку при создании нового соединения АМЗП, но ссылка не создается в течение 1 минуты после создания соединения АМЗП.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения о служебной шине и AMQP см. по следующим ссылкам.

* [Протокол AMQP служебной шины — обзор]
* [Руководство по использованию протокола AMQP 1.0]
* [Протокол AMQP служебной шины для Windows Server]

[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md
[Руководство по использованию протокола AMQP 1.0]: service-bus-amqp-protocol-guide.md
[Протокол AMQP служебной шины для Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
