---
title: Удаление актеров Службы обслуживания Azure Fabric
description: Узнайте, как вручную и полностью удалить надежных актеров и их состояние в приложении Azure Service Fabric.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645622"
---
# <a name="delete-reliable-actors-and-their-state"></a>Удаление субъектов Reliable Actors и данных их состояния
При удалении деактивированных субъектов удаляется только объект субъекта; из диспетчера состояний субъекта данные не удаляются. При повторной активации субъекта его данные снова становятся доступны в диспетчере состояний. Если субъекты, данные которых хранятся в диспетчере состояний, деактивируются и больше не активируются, может возникнуть необходимость в удалении связанных с ними данных.

В [службе субъектов](service-fabric-reliable-actors-platform.md) имеется функция удаления субъектов с помощью удаленного вызывающего объекта:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

При удалении субъекта происходит следующее (в зависимости от того, были ли он активен в момент удаления):

* **Активный субъект**
  * Субъект удаляется из списка активных субъектов и деактивируется.
  * Состояние субъекта удаляется окончательно.
* **Неактивный субъект**
  * Состояние субъекта удаляется окончательно.

Субъект не может выполнить вызов на собственное удаление при помощи одного из собственных методов. Это обусловлено тем, что нельзя удалить субъект, выполняемый в контексте вызова субъектов, так как для обеспечения однопоточного доступа среда выполнения блокирует вызов субъекта.

Дополнительные сведения о Reliable Actors см. в следующих документах:
* [Таймеры и напоминания субъекта](service-fabric-reliable-actors-timers-reminders.md)
* [Актер события](service-fabric-reliable-actors-events.md)
* [Ретрансция актера](service-fabric-reliable-actors-reentrancy.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Код образца СИ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Код Java Sample](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
