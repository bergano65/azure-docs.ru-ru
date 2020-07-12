---
title: Перечисление субъектов в Azure Service Fabric
description: Дополнительные сведения о перечислении Reliable Actors и их метаданных в приложении Service Fabric Azure с помощью примеров.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 8e462cc5fa82b8692304f58ef6cf0ea0e2db8725
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245982"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Перечисление Reliable Actors в Service Fabric
Служба Reliable Actors позволяет клиенту перечислять метаданные о размещенных службой субъектах. Так как служба субъектов — это секционированная служба с отслеживанием состояния, перечисление выполняется по каждому разделу. Так как каждая секция может содержать большое количество субъектов, перечисление возвращается как набор постраничных результатов. Страницы зацикливаются до тех пор, пока все они не будут прочитаны. В следующем примере показано, как создать список всех активных субъектов в одной секции службы субъектов:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Дальнейшие действия
* [Управление состоянием субъекта](service-fabric-reliable-actors-state-management.md)
* [Жизненный цикл субъекта и сборка мусора](service-fabric-reliable-actors-lifecycle.md)
* [Справочная документация по API субъектов](/previous-versions/azure/dn971626(v=azure.100))
* [Пример кода .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Пример кода Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
