---
title: Использование пакета SDK WebJobs Azure
description: Ознакомьтесь со сведениями о написании кода для пакета SDK WebJobs. Создание задания обработки, работающих с данными в Azure служб и служб сторонних фон на основе событий.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831798"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Использование пакета SDK WebJobs Azure для фоновой обработки на основе событий

Статья содержит рекомендации по работе с пакетом SDK веб-заданий Azure. Чтобы приступить к работе с веб-заданий, см. в разделе [приступить к работе с пакетом SDK для веб-заданий Azure для управляемых событиями фоновой обработки](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

Это основные различия между версии 3. *x* и версии 2. *x* пакета SDK веб-заданий:

* Версия 3. *x* добавлена поддержка .NET Core.
* В версии 3. *x*, необходимо явно установить расширение привязки хранилища, необходимо с помощью пакета SDK веб-заданий. В версии 2. *x*, привязки хранилища были включены в пакет SDK.
* Инструментам Visual Studio для .NET Core (3. *x*) проектов отличается от инструментарий для .NET Framework (2. *x*) проекты. Дополнительные сведения см. в разделе [разработка и развертывание веб-заданий с помощью Visual Studio: службе приложений Azure](webjobs-dotnet-deploy-vs.md).

Если это возможно, приведены примеры для версии 3. *x* и версии 2. *x*.

> [!NOTE]
> [Функции Azure](../azure-functions/functions-overview.md) основана на пакет SDK веб-заданий, и в этой статье приведены ссылки на документации по функциям Azure для некоторых разделов. Обратите внимание, эти различия между функциями и пакет SDK веб-заданий:
> * Azure функций версии 2. *x* соответствует веб-заданий SDK версии 3. *x*и функции Azure 1. *x* соответствует 2 пакета SDK для веб-заданий. *x*. Репозитории исходного кода с помощью пакета SDK веб-заданий нумерации.
> * Пример кода для функций Azure C# библиотек классов похож код пакета SDK веб-заданий, за исключением того, не требуется `FunctionName` атрибут в проекте веб-заданий SDK.
> * Некоторые типы привязки, поддерживаются только в функции, такие как HTTP (веб-перехватчики) и "Сетка событий" (который основан на HTTP).
>
> Дополнительные сведения см. в разделе [Сравнение функций и веб-заданий](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Узел веб-заданий

Узел — это контейнер среды выполнения для функций.  Он прослушивает триггеры и вызовы функций. В версии 3. *x*, узел представляет собой реализацию `IHost`. В версии 2. *x*, использовании `JobHost` объекта. Вам нужно создать экземпляр узла в коде и написать код, чтобы настроить его работу.

Это ключевое различие между непосредственно с помощью пакета SDK веб-заданий и косвенно использовать его в рамках функций Azure. В функциях Azure служба управляет узла, а узел нельзя настроить путем написания кода. Функции Azure позволяет настраивать поведение узла через параметры в файле host.json. Эти параметры представляют собой строки, не в коде, и это ограничивает типы настроек, которые можно сделать.

### <a name="host-connection-strings"></a>Строки подключения узла

Пакет SDK веб-заданий ищет строки подключения хранилища Azure и служебной шины Azure в файле local.settings.json при локальном запуске или в среде веб-задание при запуске в Azure. По умолчанию строка подключения к хранилищу, параметр с именем `AzureWebJobsStorage` является обязательным.  

Версия 2. *x* пакета SDK вы сможете использовать собственные имена для этих строк подключения или сохранить их в другом месте. Можно задать имена в коде с помощью [ `JobHostConfiguration` ], как показано ниже:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Так как версии 3. *x* использует конфигурацию по умолчанию .NET Core API-интерфейсы, никакой интерфейс API, чтобы изменить имена строк подключения.

### <a name="host-development-settings"></a>Параметры разработки узла

Вы можете запустить узел в режиме разработки, чтобы повысить эффективность локальной разработки. Ниже приведены некоторые параметры, которые изменяются при запуске в режиме разработки.

| Свойство | Параметр разработки |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` — чтобы увеличить выходные данные журнала. |
| `Queues.MaxPollingInterval`  | Низкое значение для обеспечения немедленного запуска методов очереди.  |
| `Singleton.ListenerLockPeriod` | 15 секунд для ускорения последовательной разработки. |

Процесс включения режима разработки зависит от версии пакета SDK. 

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* использует стандартные интерфейсы API ASP.NET Core. Вызовите [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) метод [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) экземпляра. Передать строку с именем `development`, как показано в этом примере:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

В классе `JobHostConfiguration` имеется метод `UseDevelopmentSettings`, который включает режим разработки.  В следующем примере показано, как использовать параметры разработки. Чтобы сделать `config.IsDevelopment` возвращают `true` при ее запуске локально значение локальной переменной с именем `AzureWebJobsEnv` со значением `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Управление одновременных подключений (версии 2. *x*)

В версии 3. *x*, ограничение числа подключений по умолчанию использует бесконечный подключений. Если по некоторым причинам вам нужно изменить это ограничение, можно использовать [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) свойство [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) класса.

В версии 2. *x*, контролировать количество одновременных подключений к узлу с помощью [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2\. *x*, следует увеличить это значение по умолчанию, равное 2 перед запуском узла веб-заданий.

Все исходящие HTTP-запросы, сделанные из функции с помощью `HttpClient` проходить через `ServicePointManager`. После достижения значения, заданного `DefaultConnectionLimit`, `ServicePointManager` запускает постановка в очередь запросов перед их отправкой. Предположим, что для ограничения `DefaultConnectionLimit` установлено значение 2 и код включает 1000 HTTP-запросов. Первоначально в ОС разрешено только два запроса. Остальные 998 поставлены в очередь, пока не появится место для них. Это значит, ваши `HttpClient` может истечь, поскольку были внесены запроса, но запрос никогда не был отправлен в ОС на конечный сервер. Таким образом, вы можете столкнуться с поведением, которое не имеет смысла: локальному клиенту `HttpClient` потребуется 10 секунд, чтобы выполнить запрос, но служба будет возвращать каждый запрос за 200 мс. 

Значение по умолчанию для приложений ASP.NET — `Int32.MaxValue`, и, вероятнее всего, для работы с веб-заданий, работающих в базовый или более поздней версии план службы приложений. Веб-заданий обычно требуется параметр Always On, и, поддерживается только основные и более поздних версий планов службы приложений.

Если ваше задание WebJob работает в бесплатном или общем плане службы приложений, приложение ограничено песочницей службы приложений, которая в настоящее время имеет такое [ограничение подключений — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Ограничения не связанного с данными подключения `ServicePointManager`, более вероятно, что будет достигнуто пороговое значение подключения "песочницы", и сайт завершит работу. В этом случае установка более низкого значения параметра `DefaultConnectionLimit`, например 50 или 100, может не допустить события выше и обеспечить достаточную пропускную способность.

Параметр нужно настроить перед выполнением HTTP-запросов. По этой причине узел веб-заданий не следует настроить его автоматически. Может быть HTTP-запросы, которые происходят перед запуском узла, которые могут привести к непредвиденному поведению. Лучше всего задать значение сразу же в вашей `Main` метод до инициализации `JobHost`, как показано ниже:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Триггеры

Функции должны быть открытыми методами и должна иметь один атрибут триггера или [ `NoAutomaticTrigger` ](#manual-triggers) атрибута.

### <a name="automatic-triggers"></a>Автоматических триггеров

Автоматические триггеры вызывают функцию в ответ на событие. Рассмотрим следующий пример функции, которая активируется сообщением, добавляется в хранилище очередей Azure. Она отвечает, чтение большого двоичного объекта из хранилища BLOB-объектов Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` Атрибут сообщает среде выполнения для вызова функции каждый раз, когда сообщение в очередь в `myqueue-items` очереди. `Blob` Атрибут сообщает среде выполнения использовать сообщение очереди для чтения большого двоичного объекта в *пример workitems* контейнера. Содержимое сообщения очереди, переданный в функцию `myQueueItem` параметра — имя большого двоичного объекта.


### <a name="manual-triggers"></a>Ручная активация

Чтобы вызвать функцию вручную, используйте `NoAutomaticTrigger` атрибута, как показано ниже:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Процесс вручную активировать функции зависит от версии пакета SDK.

#### <a name="version-3x"></a>Версия 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Входные и выходные привязки

Входные привязки предоставляют декларативный способ получения данных для кода из внешних служб или служб Azure. Выходные привязки обеспечивают способ обновления данных. [Приступить к работе](webjobs-sdk-get-started.md) статье приведен пример каждого из них.

Можно использовать возвращаемое значение метода для выходной привязки путем применения атрибута к возвращаемому значению метода. Ознакомьтесь с примером в [с помощью функции Azure возвращаемое значение](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Типы привязки

Процесс установки и управления ими типы привязки зависит от того, используете ли вы версии 3. *x* или версии 2. *x* пакета SDK. Вы найдете его установки для типа определенную привязку в разделе «Пакеты» функций Azure этим типом привязки [справочной статье](#binding-reference-information). Исключением является файлах триггер и привязки (для локальной файловой системы), который не поддерживается службой "функции Azure".

#### <a name="version-3x"></a>Версия 3. *x*

В версии 3. *x*, привязки хранилища включаются в `Microsoft.Azure.WebJobs.Extensions.Storage` пакета. Вызовите `AddAzureStorage` метод расширения в `ConfigureWebJobs` метод, как показано ниже:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод расширения `Add<binding>`, реализованный в расширении. Например, если вы хотите использовать привязки Azure Cosmos DB, установить `Microsoft.Azure.WebJobs.Extensions.CosmosDB` и вызвать `AddCosmosDB`, следующим образом:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Чтобы использовать триггер таймера или привязку файлов, являющиеся частью основных служб, вызовите методы расширения `AddTimers` или `AddFiles` соответственно.

#### <a name="version-2x"></a>Версия 2. *x*

Эти типы триггера и привязки, включены в версии 2. *x* из `Microsoft.Azure.WebJobs` пакета:

* Хранилище BLOB-объектов
* Хранилище очередей
* Хранилище таблиц

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод `Use<binding>` для объекта `JobHostConfiguration`. Например, если вы хотите использовать триггер таймера, установите `Microsoft.Azure.WebJobs.Extensions` и вызвать `UseTimers` в `Main` метод, как показано ниже:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Чтобы использовать привязку файлов, установите `Microsoft.Azure.WebJobs.Extensions` и вызовите `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

Веб-задания позволяют выполнить привязку к [`ExecutionContext`]. С помощью этой привязки вы можете получить доступ к параметру [`ExecutionContext`] в сигнатуре функции. Например, следующий код использует объект контекста для доступа к идентификатору вызова, который вы можете использовать для корреляции всех журналов, созданных данным вызовом функции.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Процесс для привязки к [ `ExecutionContext` ] зависит от версии пакета SDK.

#### <a name="version-3x"></a>Версия 3. *x*

Вызовите `AddExecutionContextBinding` метод расширения в `ConfigureWebJobs` метод, как показано ниже:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

Пакет `Microsoft.Azure.WebJobs.Extensions`, упомянутый ранее, также предоставляет специальный тип привязки, который вы можете зарегистрировать, вызвав метод `UseCore`. Эта привязка позволяет определять [ `ExecutionContext` ] параметр в сигнатуре функции, которая включается следующим образом:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Конфигурация привязки

Можно настроить поведение некоторых триггеров и привязок. В процессе настройки их зависит от версии пакета SDK.

* **Версия 3. *x*:** Задание конфигурации при `Add<Binding>` метод вызывается в `ConfigureWebJobs`.
* **Версия 2. *x*:** Набор конфигурации, установив свойства в объект конфигурации, который передается в `JobHost`.

Эти параметры, относящиеся к привязке эквивалентны параметров [файл проекта host.json](../azure-functions/functions-host-json.md) в функциях Azure.

Можно настроить следующие привязки:

* [Триггер Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Триггер концентратора событий](#event-hubs-trigger-configuration-version-3x)
* Триггер хранилища очередей
* [Привязки SendGrid](#sendgrid-binding-configuration-version-3x)
* [Триггер служебной шины](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Конфигурация триггера Azure CosmosDB (версии 3. *x*)

В этом примере показано, как настроить триггер Azure Cosmos DB:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Дополнительные сведения см. в разделе [привязки Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) статьи.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Конфигурация триггер концентраторов событий (версии 3. *x*)

В этом примере показано, как настроить триггер концентраторов событий:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Дополнительные сведения см. в разделе [привязки концентраторов событий](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) статьи.

### <a name="queue-storage-trigger-configuration"></a>Конфигурация триггера хранилища очередей

Этих примерах показано, как настроить триггер очереди службы хранилища:

#### <a name="version-3x"></a>Версия 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Дополнительные сведения см. в разделе [очередь привязка хранилища](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) статьи.

#### <a name="version-2x"></a>Версия 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Дополнительные сведения см. в разделе [версии 1.х справочнике по host.json](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Конфигурация привязки SendGrid (версии 3. *x*)

В этом примере показано, как настроить SendGrid выходная привязка:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Дополнительные сведения см. в разделе [привязка SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) статьи.

### <a name="service-bus-trigger-configuration-version-3x"></a>Конфигурация триггера служебной шины (версии 3. *x*)

В этом примере показано, как для настройки триггера служебной шины:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Дополнительные сведения см. в разделе [привязки служебной шины](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) статьи.

### <a name="configuration-for-other-bindings"></a>Конфигурация для других привязок

Некоторые типы триггера и привязки определяют свои собственные пользовательских типов конфигурации. Например триггер файла позволяет указать корневой путь для мониторинга, как в следующих примерах:

#### <a name="version-3x"></a>Версия 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Выражения привязки

В параметрах конструктора атрибутов вы можете использовать выражения, которые разрешают значения из разных источников. Например, в следующем коде путь для атрибута `BlobTrigger` создает выражение с именем `filename`. При использовании для выходной привязки `filename` разрешается к имени активирующего большого двоичного объекта.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Дополнительные сведения о выражениях привязки см. в разделе [Выражения привязки и шаблоны](../azure-functions/functions-bindings-expressions-patterns.md) в документации по решению "Функции Azure".

### <a name="custom-binding-expressions"></a>Настраиваемые выражения привязки

Иногда требуется указать имя очереди, имя большого двоичного объекта или контейнера или имени таблицы в код, а не коде. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для атрибута `QueueTrigger`.

Это можно сделать путем передачи `NameResolver` объект в `JobHostConfiguration` объекта. В параметрах конструктора атрибута привязки и триггера нужно указать специальные заполнители, тогда код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей. Чтобы определить заполнители заключить их в знак процента (%) знаки, как показано ниже:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Этот код позволяет использовать очередь с именем `logqueuetest` в тестовой среде и очередь с именем `logqueueprod` в производственной среде. Вместо фиксированного имени очереди требуется указать имя записи в коллекции `appSettings`.

По умолчанию `NameResolver` вступит в силу немедленно, если вы не укажете новый. Стандартное средство получает значения из настроек приложения или переменных среды.

Ваш `NameResolver` класс получает имя очереди из `appSettings`, как показано ниже:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Версия 3. *x*

Сопоставитель настраивается с помощью внедрения зависимостей. Для этих примеров необходим следующий оператор `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Добавить Сопоставитель, вызвав [ `ConfigureServices` ] метод расширения в [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), как показано в этом примере:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

Передать ваш `NameResolver` класс в `JobHost` объекта, как показано ниже:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Решение "Функции Azure" реализует `INameResolver`, чтобы получить значения из настроек приложения, как показано в этом примере. Когда вы используете пакет SDK WebJobs напрямую, вы можете написать специальную реализацию, которая получает значения замены заполнителя из любого выбранного источника.

## <a name="binding-at-runtime"></a>Привязка во время выполнения

Если необходимо выполнить некоторую работу в функции, прежде чем использовать атрибут привязки, такие как `Queue`, `Blob`, или `Table`, можно использовать `IBinder` интерфейс.

В следующем примере в выходной очереди создается новое сообщение с тем же содержимым, что и в сообщении входной очереди. Имя очереди вывода определяется кодом в теле функции.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Дополнительные сведения см. в разделе [Привязка во время выполнения](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) в документации по решению "Функции Azure".

## <a name="binding-reference-information"></a>Справочная информация о привязке

Документация по функциям Azure содержит справочные сведения о каждом типе привязки. Вы найдете следующие сведения в каждой статье ссылки привязки. (Этот пример построен на основе очереди хранилища.)

* [Пакеты](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Пакет, который необходимо установить для поддержки привязки в проекте веб-заданий SDK.
* [Примеры](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Примеры кода. C# Пример библиотеки класса относится к SDK веб-заданий. Просто опустив `FunctionName` атрибута.
* [Атрибуты](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Атрибуты для типа привязки.
* [Конфигурации](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Описание свойств атрибутов и параметров конструктора.
* [Использование](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Типы, которые можно привязать к и сведения о том, как работает привязка. Например: алгоритм опроса, обработка подозрительной очереди.
  
Список привязки справочные статьи, см. в разделе «Поддерживаемые привязки» в [триггеров и привязок](../azure-functions/functions-triggers-bindings.md#supported-bindings) статьи для функций Azure. В этом списке привязок HTTP, веб-перехватчики и "Сетка событий" поддерживаются только функции Azure, а не с помощью пакета SDK веб-заданий.

## <a name="disable-attribute"></a>Атрибут Disable 

[ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) Атрибут позволяет контролировать ли функцию можно активировать. 

В следующем примере Если параметр приложения `Disable_TestJob` имеет значение `1` или `True` (без учета регистра), функция не будет работать. В этом случае среда выполнения создает сообщение журнала о том, что *функция Functions.TestJob отключена*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

При изменении значения параметра приложения на портале Azure, чтобы выбрать новую настройку перезапускает веб-задания.

Атрибут может объявляться на уровне класса, метода или параметра. Имя параметра также может содержать выражения привязки.

## <a name="timeout-attribute"></a>Атрибут Timeout

[ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) Атрибут приводит к функции отменяется, если она не завершается в течение указанного времени. В следующем примере функция будет выполняться один день без атрибута времени ожидания. Время ожидания вызывает функцию для отменен после 15 секунд.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Атрибут времени ожидания на уровне класса или метода можно применить, и глобальное время ожидания можно указать с помощью `JobHostConfiguration.FunctionTimeout`. Время ожидания уровня класса или методов переопределения глобальных таймаутов.

## <a name="singleton-attribute"></a>Атрибут Singleton

[ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) Атрибут обеспечивает только один экземпляр функции выполняется, даже если существует несколько экземпляров несущего веб-приложения. Это делается с помощью [распределенных блокировки](#viewing-lease-blobs).

В этом примере только один экземпляр `ProcessImage` функция выполняется в любой момент времени:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

У некоторых триггеров есть встроенная поддержка для управления параллелизмом:

* **QueueTrigger**. Задайте для параметра `JobHostConfiguration.Queues.BatchSize` значение `1`.
* **ServiceBusTrigger**. Задайте для параметра `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` значение `1`.
* **FileTrigger**. Задайте для параметра `FileProcessor.MaxDegreeOfParallelism` значение `1`.

Эти параметры можно использовать для обеспечения отдельного выполнения функции в одном экземпляре. Убедитесь, что только один экземпляр функции запускается, когда веб-приложение масштабируется до нескольких экземпляров, применить блокировку одноэлементный прослушиватель уровня функции (`[Singleton(Mode = SingletonMode.Listener)]`). Прослушиватель блокировок при запуске JobHost. Если все три горизонтально масштабированные экземпляры запускаются одновременно, блокировки требует только один из экземпляров, а запускается только один прослушиватель.

### <a name="scope-values"></a>Значения области

Можно указать *область значениевыражения/* на единственный экземпляр. Значение выражения и гарантирует, что сериализуются все выполнения функции в определенной области. Реализация более детализированную блокировку таким образом можно разрешить для определенного уровня параллелизма для функции во время сериализации другим вызовам, с учетом требований. Например, в следующем коде выражение области привязывается к `Region` значение входящего сообщения. В ходе очередь содержит три сообщения в регионах, Восток, Восток и запад соответственно, сообщения, имеющие регион, восток выполняются последовательно, когда сообщение с регионом, Запад выполняется параллельно с ними в восточной части.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

Область по умолчанию для блокировки — `SingletonScope.Function`, то есть область блокировки (путь аренды BLOB-объектов) привязывается к полное имя функции. Чтобы заблокировать по функциям, укажите `SingletonScope.Host` и используйте имя области идентификатора, которое является одинаковым для всех функций, которые вы не хотите выполнять одновременно. В следующем примере выполняется только один экземпляр `AddItem` или `RemoveItem`:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Просмотр арендуемых больших двоичных объектов

Пакет SDK WebJobs использует аренды [больших двоичных объектов Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) для реализации распределенной блокировки. Аренды BLOB-объекты, используемые одноэлементный можно найти в `azure-webjobs-host` контейнера в `AzureWebJobsStorage` учетной записи хранения по пути «блокировки». Например, путь к арендуемому большому двоичному объекту для первого примера `ProcessImage`, рассмотренного ранее, должен быть таким: `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Все пути включают идентификатор JobHost, в данном случае 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Асинхронные функции

Сведения о том, как функции асинхронного кода, см. в разделе [документации по функциям Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Токены отмены

Информацию о том, как обрабатывать маркеры отмены, см. в документации по решению "Функции Azure" в разделе [Токены отмены](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Выполнение на нескольких экземплярах

Если ваше веб-приложение работает в нескольких экземплярах, на каждом экземпляре запускается непрерывный WebJob, который прослушивает триггеры и вызывает функции. Различные привязки триггера предназначены для эффективной совместной работы в экземплярах, чтобы масштабирование до большего количества экземпляров позволяло обрабатывать большую нагрузку.

Триггеры очереди и BLOB-объектов автоматически предотвратить функцию обработки сообщения очереди или большого двоичного объекта более одного раза; функции не имеют должны быть идемпотентными.

Триггер таймера автоматически гарантирует, что запускается только один экземпляр таймера, поэтому вы не получаете больше одного экземпляра функции, запущенного в заданное время.

Если вы хотите убедиться, что только один экземпляр функции выполняется даже в том случае, если существует несколько экземпляров несущего веб-приложения, можно использовать [ `Singleton` ](#singleton-attribute) атрибута.

## <a name="filters"></a>Фильтры

Функциональные фильтры (предварительная версия) обеспечивают способ настройки конвейера выполнения WebJobs с помощью вашей собственной логики. Фильтры, аналогичны [фильтрует ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Их можно реализовать как декларативные атрибуты, примененные к функций или классов. Дополнительные сведения см. на странице о [фильтрах функции](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

Мы рекомендуем использовать платформу ведения журналов, который был разработан для ASP.NET. [Приступить к работе](webjobs-sdk-get-started.md) статье показано, как его использовать. 

### <a name="log-filtering"></a>Фильтрация журнала

Каждый журнал, созданный экземпляром `ILogger`, имеет связанные параметры `Category` и `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) представляет собой перечисление, а код целого числа обозначают относительную важность:

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4\. |
|критические ошибки.    | 5 |
|Нет        | 6 |

Независимо друг от друга, можно отфильтровать каждой категории с определенным [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Например, вы можете просмотреть все журналы для обработки триггера большого двоичного объекта, но только `Error` и выше для всего остального.

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* пакета SDK использует фильтры, встроенные в .NET Core. Класс `LogCategories` позволяет определить категории для конкретных функций, триггеров или пользователей. Он также определяет фильтры для состояний конкретного узла, например `Startup` и `Results`. Это позволяет точно настроить выходные данные журнала. Если в определенных категориях не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

Добавьте в `LogCategories` следующий оператор:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

В следующем примере создается фильтр, который по умолчанию, фильтрует все журналы в `Warning` уровень. `Function` И `results` категорий (эквивалентно `Host.Results` в версии 2. *x*) будут отфильтрованы по `Error` уровень. Фильтр сравнивает текущую категорию со всеми зарегистрированными уровнями в экземпляре `LogCategories` и выбирает самое длинное совпадение. Это означает, что `Debug` зарегистрировались уровень `Host.Triggers` соответствует `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

В версии 2. *x* пакета SDK, используйте `LogCategoryFilter` класс для контроля фильтрации. `LogCategoryFilter` Имеет `Default` свойство с исходным значением `Information`, это означает, что все сообщения в `Information`, `Warning`, `Error`, или `Critical` уровни вошедшим в систему, но сообщения в `Debug` или `Trace` уровни фильтруются немедленно.

Как и в `LogCategories` в версии 3. *x*, `CategoryLevels` свойство позволяет указать уровни ведения журнала для определенной категории, позволяя вам оптимизировать выходные данные журнала. Если в словаре `CategoryLevels` не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

В следующем примере создается фильтр, который по умолчанию фильтрует все журналы на уровне `Warning`. `Function` И `Host.Results` категории фильтруются в `Error` уровень. Фильтр `LogCategoryFilter` сравнивает текущую категорию со всеми зарегистрированными уровнями `CategoryLevels` и выбирает самое длинное совпадение. Поэтому `Debug` зарегистрировались уровень `Host.Triggers` будет соответствовать `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Пользовательские данные телеметрии для Application Insights

Процесс реализации пользовательской телеметрии для [Application Insights](../azure-monitor/app/app-insights-overview.md) зависит от версии пакета SDK. Чтобы узнать, как настроить Application Insights, ознакомьтесь с разделом [Добавление журнала Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Версия 3. *x*

Так как версии 3. *x* пакета SDK веб-заданий, полагается на .NET Core, больше не предоставляется универсальный узел фабрики пользовательские данные телеметрии. Но можно добавить пользовательские данные телеметрии в конвейер, с помощью внедрения зависимостей. Для примеров в этом разделе необходимы следующие операторы `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Следующая пользовательская реализация [`ITelemetryInitializer`] позволяет добавлять собственную [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) в [`TelemetryConfiguration`] по умолчанию.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Вызовите [`ConfigureServices`] в конструкторе, чтобы добавить пользовательский [`ITelemetryInitializer`] в конвейер.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Когда [`TelemetryConfiguration`] создана, все зарегистрированные типы [`ITelemetryInitializer`] включены. Дополнительные сведения см. в разделе [API Application Insights для пользовательских событий и метрик](../azure-monitor/app/api-custom-events-metrics.md).

В версии 3. *x*, больше не нужно очистить [ `TelemetryClient` ] остановки узла. Система внедрения зависимостей .NET Core автоматически удаляет зарегистрированный `ApplicationInsightsLoggerProvider`, который очищает [`TelemetryClient`].

#### <a name="version-2x"></a>Версия 2. *x*

В версии 2. *x*, [ `TelemetryClient` ] внутренне созданные поставщиком Application Insights для веб-заданий SDK используют [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Когда конечная точка Application Insights недоступна или регулирует входящие запросы, этот канал [сохраняет запросы в файловой системе веб-приложения и позже повторно отправляет их](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] создается классом, который реализует `ITelemetryClientFactory`. По умолчанию это [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Если вы хотите изменить любую часть конвейера Application Insights, вы можете указать свою фабрику `ITelemetryClientFactory`, и узел будет использовать ваш класс для создания клиента [`TelemetryClient`]. Например, этот код переопределяет `DefaultTelemetryClientFactory` для изменения свойства `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings` Настраивает объект [Адаптивная выборка](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Это означает, что в некоторых сценариях с большим объемом, Application Insights отправляет выбранного подмножества данных телеметрии на сервер.

После создания фабрики данных телеметрии, можно передавать его в поставщике ведения журнала Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>Дальнейшие действия

В этой статье предоставлены фрагменты кода, показывающие, как для обработки обычных сценариев для работы с пакетом SDK веб-заданий. Полные примеры см. на странице с примерами [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[TelemetryConfiguration]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[«JobHostConfiguration»]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
