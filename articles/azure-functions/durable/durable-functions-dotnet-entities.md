---
title: Руководство разработчика по долгосрочным объектам в .NET - Функции Azure
description: Как работать с прочными сущностями в .NET с расширением долгосрочных функций для функций Azure.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278133"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Руководство разработчика по долгосрочным объектам в .NET

В этой статье мы подробно описываем доступные интерфейсы для разработки прочных сущностей с помощью .NET, включая примеры и общие рекомендации. 

Функции сущности предоставляют разработчикам серверных приложений удобный способ организации состояния приложений как коллекции мелкозернистых сущностей. Для получения более подробной информации [Durable Entities: Concepts](durable-functions-entities.md) об основных концепциях см.

В настоящее время мы предлагаем два AA для определения сущностей:

- **Синтаксис на основе классов** представляет сущности и операции как классы и методы. Этот синтаксис производит легко читаемый код и позволяет вызывать операции в проверенных типах образом через интерфейсы. 

- **Синтаксис на основе функций** — это интерфейс более низкого уровня, который представляет сущности как функции. Он обеспечивает точный контроль над тем, как диспетчеризируются операции сущности, и как управляется состояние сущности.  

Эта статья посвящена в первую очередь классу синтаксиса, так как мы ожидаем, что он будет лучше подходит для большинства приложений. Однако [синтаксис на основе функций](#function-based-syntax) может быть подходящим для приложений, которые хотят определить или управлять своими абстракциями для состояния сущности и операций. Кроме того, может быть целесообразным для реализации библиотек, которые требуют обобщения, которые в настоящее время не поддерживаются коллективным синтаксисом. 

> [!NOTE]
> Синтаксис на основе классов — это всего лишь слой поверх синтаксиса на основе функций, поэтому оба варианта могут быть использованы в одном приложении. 
 
## <a name="defining-entity-classes"></a>Определение классов сущности

Следующим примером является реализация `Counter` сущности, которая хранит одно значение типа целых, `Get`и `Delete`предлагает четыре операции `Add`, `Reset`и .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Функция `Run` содержит шаблон, необходимый для использования синтаксиса на основе класса. Это должна быть *статическая* функция Azure. Он выполняет один раз для каждого сообщения операции, которое обрабатывается сущностью. Когда `DispatchAsync<T>` объект вызывается и сущность уже не в памяти, она строит объект типа `T` и заполняет его поля от последнего упорствованного JSON, найденного в хранилище (если таковые имеется). Затем он вызывает метод с соответствующим именем.

> [!NOTE]
> Состояние сущности, основанной на классе, **создается неявно** до того, как сущность `Entity.Current.DeleteState()`обрабатывает операцию, и может быть **удалено явно** в операции путем вызова .

### <a name="class-requirements"></a>Требования к классу
 
Классы сущности — это POCPo (обычные старые объекты CLR), которые не требуют специальных суперклассов, интерфейсов или атрибутов. Но:

- Класс должен быть конструктивным (см. [Конструкцию entity).](#entity-construction)
- Класс должен быть JSON-серизмируемым (см. [Сериализацию Entity).](#entity-serialization)

Кроме того, любой метод, который должен быть использован в качестве операции, должен удовлетворять дополнительным требованиям:

- Операция должна иметь не более одного аргумента и не должна иметь никаких перегрузок или общих аргументов типа.
- Операция, предназначенная для вызова из оркестровки с помощью интерфейса, должна вернуться `Task` или `Task<T>`.
- Аргументы и значения возврата должны быть серийными значениями или объектами.

### <a name="what-can-operations-do"></a>Что могут делать операции?

Все операции сущности могут читать и обновлять состояние объекта, а изменения в состоянии автоматически сохраняются в хранилище. Кроме того, операции могут выполнять внешние вво/огню или другие вычисления в общих пределах, общих для всех функций Azure.

Операции также имеют доступ к `Entity.Current` функциональности, предоставляемой контекстом:

* `EntityName`: имя в настоящее время исполнительной сущности.
* `EntityKey`ключ в настоящее время исполнительной сущности.
* `EntityId`: идентификатор в настоящее время исполнительной сущности (включает имя и ключ).
* `SignalEntity`: отправляет единое сообщение объекту.
* `CreateNewOrchestration`: начинается новая оркестровка.
* `DeleteState`: удаляет состояние этого объекта.

Например, мы можем изменить объект счетчика, чтобы она начала оркестровку, когда счетчик достигает 100, и передает идентификатор сущности в качестве вхонного аргумента:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Доступ к объектам напрямую

Объекты, основанные на классах, могут быть доступны напрямую, используя явные имена строк для сущности и ее операций. Ниже приведены некоторые примеры; для более глубокого объяснения основополагающих понятий (таких как сигналы против вызовов) смотрите обсуждение в [сущностях доступа](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Там, где это возможно, мы рекомендуем [доступ к объектам через интерфейсы,](#accessing-entities-through-interfaces)поскольку он обеспечивает более проверку типов.

### <a name="example-client-signals-entity"></a>Пример: сущность сигналов клиента

Следующая функция Azure Http реализует операцию DELETE с использованием конвенций REST. Он посылает сигнал удаления встречной организации, ключ которой передается в пути URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Пример: клиент считывает состояние сущности

Следующая функция Azure Http реализует операцию GET с использованием конвенций REST. В нем считывается текущее состояние встречной сущности, ключ которой передается в пути URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Объект, `ReadEntityStateAsync` возвращенный, — это всего лишь локальная копия, т.е. снимок состояния сущности с более ранней точки времени. В частности, он может быть устаревшим, и изменение этого объекта не влияет на фактическое образование. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Пример: оркестровка сначала сигнализирует, а затем вызывает сущность

Следующая оркестровка сигнализирует встречной сущности, чтобы приравнять его, а затем вызывает ту же сущность, чтобы прочитать ее последнее значение.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Доступ к объектам через интерфейсы

Интерфейсы могут использоваться для доступа к объектам через генерируемые прокси-объекты. Этот подход гарантирует, что тип имени и аргумента операции соответствует реализованным. Мы рекомендуем использовать интерфейсы для доступа к объектам, когда это возможно.

Например, мы можем изменить пример счетчика следующим образом:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Классы сущности и интерфейсы сущности аналогичны зерен и зернистых интерфейсов, популяризуемых [Orleans.](https://www.microsoft.com/research/project/orleans-virtual-actors/) Для получения дополнительной информации о сходствах и различиях между durable Entities и Orleans, [см.](durable-functions-entities.md#comparison-with-virtual-actors)

Помимо проверки типа, интерфейсы полезны для лучшего разделения проблем в приложении. Например, поскольку сущность может реализовать несколько интерфейсов, одна сущность может обслуживать несколько ролей. Кроме того, поскольку интерфейс может быть реализован несколькими объектами, общие шаблоны связи могут быть реализованы как многоразовые библиотеки.

### <a name="example-client-signals-entity-through-interface"></a>Пример: сущность сигналов клиента через интерфейс

Клиентский код `SignalEntityAsync<TEntityInterface>` может использоваться для `TEntityInterface`отправки сигналов объектам, которые реализуют. Пример:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

В этом примере `proxy` параметр представляет собой `ICounter`динамически сгенерированный `Delete` экземпляр, который внутренне переводит вызов в сигнал.

> [!NOTE]
> `SignalEntityAsync` AA можно использовать только для односторонних операций. Даже если операция `Task<T>`возвращается, `T` значение параметра всегда `default`будет нулевым или, а не фактический результат.
Например, нет смысла сигнализировать `Get` об операции, так как значение не возвращается. Вместо этого клиенты `ReadStateAsync` могут использовать либо для прямого доступа к состоянию `Get` счетчика, либо запускать функцию оркестратора, которая вызывает операцию. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Пример: оркестровка сначала сигнализирует, а затем вызывает сущность через прокси

Для вызова или сигнала сущности `CreateEntityProxy` из оркестровки можно использовать, наряду с типом интерфейса, для создания прокси для сущности. Этот прокси может быть использован для вызова или сигнала операций:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Неявно любые операции, которые возвращаются, `void` сигнализируются, и любые операции, которые возвращаются `Task` или `Task<T>` вызываются. Можно изменить это поведение по умолчанию и сигнальные `SignalEntity<IInterfaceType>` операции, даже если они возвращают задачу, используя метод явно.

### <a name="shorter-option-for-specifying-the-target"></a>Более короткий вариант для определения цели

При вызове или сигнализации сущности с помощью интерфейса первый аргумент должен указать целевую сущность. Цель может быть указана либо путем указания идентификатора сущности, либо, в тех случаях, когда есть только один класс, который реализует сущность, только ключ сущности:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Если указан только ключ сущности и уникальная реализация не `InvalidOperationException` может быть найдена во время выполнения, брошена. 

### <a name="restrictions-on-entity-interfaces"></a>Ограничения на интерфейсы сущностей

Как обычно, все типы параметров и возврата должны быть JSON-серизобными. В противном случае исключения сериализации выбрасываются во время выполнения.

Мы также вменяем некоторые дополнительные правила:
* Интерфейсы сущности должны определять только методы.
* Интерфейсы сущности не должны содержать общие параметры.
* Методы интерфейса entity не должны иметь более одного параметра.
* Методы интерфейса `void`entity `Task`должны вернуться, или`Task<T>` 

Если какие-либо из этих `InvalidOperationException` правил нарушаются, он выбрасывается во время `SignalEntity` выполнения, когда интерфейс используется в качестве аргумента типа или `CreateProxy`. Сообщение исключения объясняет, какое правило было нарушено.

> [!NOTE]
> Возвращается `void` интерфейс методы могут быть сигнализированы (в одну сторону), не называется (двусторонний). Методы интерфейса возвращаются `Task` или `Task<T>` могут быть вызваны или сигнализируются. При вызове они возвращают результат операции или повторно выбрасывают исключения, брошенные операцией. Однако при сигнализации они не возвращают фактический результат или исключение из операции, а просто значение по умолчанию.

## <a name="entity-serialization"></a>Сериализация сущности

Поскольку состояние сущности надежно сохраняется, класс сущности должен быть сериализируемым. Для этой цели в runtime Durable Functions используется [Json.NET](https://www.newtonsoft.com/json) библиотека, которая поддерживает ряд политик и атрибутов для управления процессом сериализации и десериализации. Наиболее часто используемые типы данных (включая массивы и типы сбора) уже являются сериализуемыми и могут быть легко использованы для определения состояния долговременных сущностей.

Например, Json.NET могут легко сериализировать и десериализировать следующий класс:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Атрибуты сериализации

В приведенном выше примере мы решили включить несколько атрибутов, чтобы сделать базовую сериализацию более видимой:
- Мы аннотируем класс, `[JsonObject(MemberSerialization.OptIn)]` чтобы напомнить нам, что класс должен быть сериализируемым, и сохранять только элементы, которые явно помечены как свойства JSON.
-  Мы аннотировать поля, которые будут `[JsonProperty("name")]` упорствовать, чтобы напомнить нам, что поле является частью упорного состояния сущности, и указать имя свойства, которое будет использоваться в представлении JSON.

Однако эти атрибуты не требуются; другие конвенции или атрибуты разрешены до тех пор, пока они работают с Json.NET. Например, можно `[DataContract]` использовать атрибуты или вообще не использовать атрибуты:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

По умолчанию имя класса *не* сохраняется как часть представления JSON: `TypeNameHandling.None` то есть мы используем в качестве параметра по умолчанию. Это поведение по умолчанию `JsonObject` `JsonProperty` может быть отменено с помощью или атрибутов.

### <a name="making-changes-to-class-definitions"></a>Внесение изменений в определения классов

Некоторая осторожность требуется при внесении изменений в определение класса после запуска приложения, поскольку сохраненный объект JSON может больше не соответствовать новому определению класса. Тем не менее, часто можно правильно справиться с изменением форматов данных `JsonConvert.PopulateObject`до тех пор, как один понимает процесс десериализации используется .

Например, вот несколько примеров изменений и их влияния:

1. При добавлении нового свойства, которое не присутствует в сохраненном JSON, оно принимает его значение по умолчанию.
1. Если свойство удалено, которое присутствует в сохраненном JSON, предыдущее содержимое теряется.
1. Если свойство переименовано, эффект как бы удаляет старое и добавляет новый.
1. Если тип свойства изменен, поэтому он больше не может быть десериализован из хранимых JSON, выбрасывается исключение.
1. Если тип свойства изменен, но он все еще может быть десериализован из хранящегося JSON, он сделает это.

Есть много вариантов, доступных для настройки поведения Json.NET. Например, чтобы заставить исключение, если сохраненный JSON содержит поле, которое не присутствует в классе, укажите атрибут `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. Также можно написать пользовательский код для десериализации, который может читать JSON, хранящийся в произвольных форматах.

## <a name="entity-construction"></a>Конструкция сущности

Иногда мы хотим установить больший контроль над тем, как объекты сущности строятся. Теперь мы описываем несколько вариантов изменения поведения по умолчанию при построении объектов сущности. 

### <a name="custom-initialization-on-first-access"></a>Пользовательская инициализация первого доступа

Иногда нам нужно выполнить специальную инициализацию перед отправкой операции объекту, который никогда не был доступен или который был удален. Чтобы указать это поведение, можно `DispatchAsync`добавить условный перед:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Привязки в классах сущностей

В отличие от обычных функций, методы класса сущностей не имеют прямого доступа к входным и выходным привязким. Вместо этого данные привязки должны быть записаны в объявлении функции точки входа, а затем переданы в метод `DispatchAsync<T>`. Любые объекты, передаваемые в `DispatchAsync<T>`, будут автоматически переданы в конструктор класса сущностей в виде аргумента.

В следующем примере показано, как можно сделать ссылку `CloudBlobContainer` из [входной привязки BLOB-объекта](../functions-bindings-storage-blob-input.md) доступной для сущности на основе класса.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Дополнительные сведения о привязках в Функциях Azure см. в статье[Azure Functions triggers and bindings concepts](../functions-triggers-bindings.md) (Основные понятия триггеров и привязок в Функциях Azure).

### <a name="dependency-injection-in-entity-classes"></a>Инъекция зависимости в классах сущностей

Классы сущностей [поддерживают внедрение зависимостей Функций Azure](../functions-dotnet-dependency-injection.md). В следующем примере показано, как зарегистрировать службу `IHttpClientFactory` в сущность на основе класса.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

В следующем фрагменте кода показано, как встроить внедренную службу в класс сущностей.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Чтобы избежать проблем с сериализацией, не забудьте исключить поля, предназначенные для хранения впрыскиваемых значений, из сериализации.

> [!NOTE]
> В отличие от внедрения конструктора в обычных Функциях Azure .NET, метод точки входа функций для сущностей на основе класса *необходимо* объявить `static`. Объявление нестатической точки входа функции может привести к конфликтам между обычным инициализатором объектов Функций Azure и инициализатором объектов Устойчивых сущностей.

## <a name="function-based-syntax"></a>Синтаксис на основе функций

До сих пор мы сосредоточились на коллективном синтаксисе, так как мы ожидаем, что он будет лучше подходит для большинства приложений. Тем не менее, синтаксис на основе функций может быть подходящим для приложений, которые хотят определить или управлять своими абстракциями для состояния сущности и операций. Кроме того, может быть целесообразным при реализации библиотек, требующих обобщения, которые в настоящее время не поддерживаются коллективным синтаксисом. 

С помощью синтаксиса на основе функций сущности функция entity явно обрабатывает диспетчериз-диспетчериз- операцию и явно управляет состоянием сущности. Например, в следующем коде отображается объект *Counter,* реализованный с помощью синтаксиса на основе функций.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Объект контекста сущности

Функциональность, специфичная для конкретной сущности, может быть доступна через контекстный объект типа. `IDurableEntityContext` Этот контекстный объект доступен в качестве параметра для функции `Entity.Current`сущности и через свойство async-местного.

Следующие участники предоставляют информацию о текущей операции и позволяют указать значение возврата. 

* `EntityName`: имя в настоящее время исполнительной сущности.
* `EntityKey`ключ в настоящее время исполнительной сущности.
* `EntityId`: идентификатор в настоящее время исполнительной сущности (включает имя и ключ).
* `OperationName`: название текущей операции.
* `GetInput<TInput>()`: получает вход для текущей операции.
* `Return(arg)`: возвращает значение в оркестровку, которая называется операцией.

Следующие участники управляют состоянием сущности (создают, читают, обновляют, удаляют). 

* `HasState`: существует ли сущность, то есть имеет какое-то состояние. 
* `GetState<TState>()`: получает текущее состояние объекта. Если он еще не существует, он создается.
* `SetState(arg)`: создает или обновляет состояние объекта.
* `DeleteState()`: удаляет состояние объекта, если оно существует. 

Если состояние, `GetState` возвращенное объектом, является объектом, оно может быть непосредственно изменено кодом приложения. Существует не нужно `SetState` звонить снова в конце (но и никакого вреда). Если `GetState<TState>` вызывается несколько раз, необходимо использовать один и тот же тип.

Наконец, следующие участники используются для сигнала других сущностей или запуска новых согласований:

* `SignalEntity(EntityId, operation, input)`: отправляет единое сообщение объекту.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: начинается новая оркестровка.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Узнайте о концепциях сущности](durable-functions-entities.md)
