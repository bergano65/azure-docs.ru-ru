---
title: Устранение ошибок AMQP в служебной шине Azure | Документация Майкрософт
description: Предоставляет список ошибок AMQP может получать при использовании служебной шины Azure и вызвать ошибок.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58910050"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Ошибки AMQP в служебной шине Azure
Эта статья содержит некоторые ошибки, полученный при использовании AMQP со служебной шиной Azure. Они все стандартные поведения службы. Их можно избежать с помощью обращений отправки и получения подключения/ссылки, которая автоматически восстанавливает ключа подключения.

## <a name="link-is-closed"></a>Ссылка закрыт 
Вы увидите следующую ошибку при AMQP-подключение: ссылка active, но не вызывает (например, отправлять или получать) выполняются с помощью ссылки на 10 минут. Таким образом закрывается ссылку. Подключение по-прежнему открыт.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Соединение закрыто
Видите следующую ошибку в соединении AMQP при всех ссылок в соединении были закрыты, так как не выполнялось никаких действий (в режиме простоя) и новой связи не был создан в течение 5 минут.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Ссылка не создается. 
Эта ошибка возникает в том случае, когда создается новое подключение AMQP, но в пределах 1 минуты для создания соединения AMQP не создается связь.

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
