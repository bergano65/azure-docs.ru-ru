---
title: Удаление субъектов Azure Service Fabric | Документация Майкрософт
description: Узнайте, как вручную удалить субъекты Reliable Actors в Service Fabric и данные их состояния.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726610"
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
* [События субъекта](service-fabric-reliable-actors-events.md)
* [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Пример кода Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
