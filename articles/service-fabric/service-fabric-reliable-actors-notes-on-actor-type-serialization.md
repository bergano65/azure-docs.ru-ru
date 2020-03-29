---
title: Надежные актеры заметки о сериализации типа актера
description: В этой статье приведены сведения о базовых требованиях к определению сериализуемых классов, которые можно использовать для определения интерфейсов и состояний Reliable Actors в Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349309"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Примечания о сериализации типов надежных субъектов Service Fabric
Аргументы всех методов, типы результатов задач, возвращенных каждым методом в интерфейсе субъекта, и объекты, хранящиеся в государственном менеджере субъекта, должны быть [контрактом данных серийно.](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) Это также относится к аргументам методов, определенных в [интерфейсах событий субъекта](service-fabric-reliable-actors-events.md). (Методы интерфейсов для событий субъектов всегда возвращают значение void.)

## <a name="custom-data-types"></a>Пользовательские типы данных
В этом примере приведенный ниже интерфейс субъекта определяет метод, возвращающий пользовательский тип данных `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Этот интерфейс реализован субъектом, который использует диспетчер состояния для хранения объекта `VoicemailBox`.

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

В этом примере объект `VoicemailBox` сериализуется в следующих случаях.

* Объект передается между экземпляром субъекта и вызывающим объектом.
* Объект сохраняется в диспетчере состояния, где он сохраняется на диске и реплицируется на другие узлы.

Платформа Reliable Actors использует сериализацию DataContract. Таким образом, пользовательские объекты данных и их члены должны быть аннотированы атрибутами **DataContract** и **DataMember** соответственно.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Дальнейшие действия
* [Актер жизненный цикл и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
* [Таймеры и напоминания субъекта](service-fabric-reliable-actors-timers-reminders.md)
* [Актер события](service-fabric-reliable-actors-events.md)
* [Ретрансция актера](service-fabric-reliable-actors-reentrancy.md)
* [Полиморфизм субъекта и объектно-ориентированные шаблоны проектирования](service-fabric-reliable-actors-polymorphism.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
