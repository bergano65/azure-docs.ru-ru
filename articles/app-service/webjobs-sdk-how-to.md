---
title: Как использовать WebJobs SDK
description: Ознакомьтесь со сведениями о написании кода для пакета SDK WebJobs. Создавайте фоновые задания, управляемые событиями, которые получают доступ к данным в службах Azure и сторонних сервисах.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5a8d5f96449cfecd4628c38fa2788a1e06e96b07
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758888"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Использование пакета SDK WebJobs Azure для фоновой обработки на основе событий

В этой статье содержатся рекомендации о том, как работать с SDK Azure WebJobs. Чтобы начать работу с WebJobs сразу же, смотрите [Начало работы с Azure WebJobs SDK для обработки фона, управляемого событиями.](webjobs-sdk-get-started.md) 

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

Таковы основные различия между версиями 3. *x* и версия 2. *x* из WebJobs SDK:

* Версия 3. *x* добавляет поддержку для .NET Core.
* В версии 3. *x,* вам необходимо четко установить расширение связывания хранилища, требуемое WebJobs SDK. В версии 2. *x,* привязки хранилища были включены в SDK.
* Инструментальная студия визуальных студий для .NET Core (3.* x)* проекты отличаются от инструментария для .NET Framework (2.* x)* проектов. Чтобы узнать больше, смотрите [Разработка и развертывание WebJobs с помощью Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md).

По возможности для обеих версий 3 приводятся примеры. *x* и версия 2. *x*.

> [!NOTE]
> [Функции Azure](../azure-functions/functions-overview.md) построены на SDK WebJobs, и в этой статье содержатся ссылки на документацию Azure Functions по некоторым темам. Обратите внимание на эти различия между функциями и SDK WebJobs:
> * Azure Функции версия 2. *x* соответствует версии WebJobs SDK 3. *x,* и Функции Azure 1. *x* соответствует WebJobs SDK 2. *x*. Репозитории исходного кода используют нумеровку WebJobs SDK.
> * Пример кода для библиотек класса Azure Functions C похож на код WebJobs `FunctionName` SDK, за исключением того, что вам не нужен атрибут в проекте WebJobs SDK.
> * Некоторые типы связывания поддерживаются только в функциях, таких как HTTP (Webhooks) и Event Grid (которая основана на HTTP).
>
> Дополнительные сведения см. в разделе [Сравнение функций и веб-заданий](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Узел веб-заданий

Узел — это контейнер среды выполнения для функций.  Он прослушивает триггеры и вызовы функций. В версии 3. *x,* хост является `IHost`реализацией . В версии 2. *x,* вы `JobHost` используете объект. Вам нужно создать экземпляр узла в коде и написать код, чтобы настроить его работу.

Это ключевое различие между прямым использованием SDK WebJobs и косвенным использованием его через функции Azure. В Azure Functions служба управляет узлами, и вы не можете настроить узел, написав код. Это решение позволяет настроить поведение узла через настройки в файле host.json. Эти параметры являются строками, а не кодом, и это ограничивает виды настроек, которые вы можете сделать.

### <a name="host-connection-strings"></a>Строки подключения узла

SDK WebJobs ищет строки подключения к системе хранения azure и Azure Service Bus в файле local.settings.json при локальном запуске или в среде WebJob при запуске в Azure. По умолчанию требуется указана `AzureWebJobsStorage` настройка строки соединения хранилища.  

Версия 2. *x* sDK позволяет использовать собственные имена для этих строк соединения или хранить их в другом месте. Вы можете установить имена [`JobHostConfiguration`]в коде, используя, как показано здесь:

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

Потому что версия 3. *x* использует API конфигурации конфигурации .NET Core, нет API для изменения имен строк соединения.

### <a name="host-development-settings"></a>Параметры разработки узла

Вы можете запустить узел в режиме разработки, чтобы повысить эффективность локальной разработки. Вот некоторые параметры, которые изменяются при запуске в режиме разработки:

| Свойство. | Параметр разработки |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` — чтобы увеличить выходные данные журнала. |
| `Queues.MaxPollingInterval`  | Низкое значение для обеспечения немедленного запуска методов очереди.  |
| `Singleton.ListenerLockPeriod` | 15 секунд для ускорения последовательной разработки. |

Процесс включения режима разработки зависит от версии SDK. 

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* использует стандартные ASP.NET основных AIS. Вызовите [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) метод [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) на экземпляре. Передайте строку с именем, `development`как в этом примере:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

В классе `JobHostConfiguration` имеется метод `UseDevelopmentSettings`, который включает режим разработки.  В следующем примере показано, как использовать параметры разработки. Чтобы `config.IsDevelopment` сделать возврат, `true` когда он работает локально, установите переменную локальной среды, названную `AzureWebJobsEnv` `Development`значением.

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Управление одновременными соединениями (версия 2.* x*)

В версии 3. *x,* ограничение соединения по умолчанию до бесконечных соединений. Если по какой-либо причине необходимо изменить [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) этот лимит, вы можете использовать свойство [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) класса.

В версии 2. *x,* вы управляете числом одновременных подключений к хостам с помощью [API ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) В 2. *x,* вы должны увеличить это значение от по умолчанию 2 перед запуском webJobs хост.

Все исходящие запросы HTTP, которые `HttpClient` вы `ServicePointManager`делаете из функции, используя поток через. После достижения значения, `DefaultConnectionLimit`установленного в, `ServicePointManager` начинается очереди запросов перед отправкой их. Предположим, что для ограничения `DefaultConnectionLimit` установлено значение 2 и код включает 1000 HTTP-запросов. Первоначально в ОС разрешено только два запроса. Остальные 998 стоят в очереди, пока нет места для них. Это означает, что ваш `HttpClient` может тайм-аут, потому что он, кажется, сделал запрос, но запрос никогда не был отправлен ОС на сервер назначения. Таким образом, вы можете столкнуться с поведением, которое не имеет смысла: локальному клиенту `HttpClient` потребуется 10 секунд, чтобы выполнить запрос, но служба будет возвращать каждый запрос за 200 мс. 

Значение по умолчанию `Int32.MaxValue`для ASP.NET приложений, и это, вероятно, хорошо работает для WebJobs работает в базовом или более высоком плане службы приложений. WebJobs обычно нуждаются в настройках Always On, и это поддерживается только основными и более высокими планами обслуживания приложений.

Если ваше задание WebJob работает в бесплатном или общем плане службы приложений, приложение ограничено песочницей службы приложений, которая в настоящее время имеет такое [ограничение подключений — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). С несвязанным ограничением `ServicePointManager`соединения в, это более вероятно, что порог соединения песочницы будет достигнут и сайт будет закрыт. В этом случае установка более низкого значения параметра `DefaultConnectionLimit`, например 50 или 100, может не допустить события выше и обеспечить достаточную пропускную способность.

Параметр нужно настроить перед выполнением HTTP-запросов. По этой причине хост WebJobs не должен автоматически корректировать настройки. До запуска узла могут возникать запросы HTTP, что может привести к неожиданному поведению. Наилучший подход заключается в том, чтобы установить значение сразу в методе `Main` до инициализации, `JobHost`как показано здесь:

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

Функции должны быть общедоступными методами [`NoAutomaticTrigger`](#manual-triggers) и должны иметь один триггерный атрибут или атрибут.

### <a name="automatic-triggers"></a>Автоматические триггеры

Автоматические триггеры вызывают функцию в ответ на событие. Рассмотрим пример функции, вызванной сообщением, добавленным в хранилище Очереди Azure. Он отвечает, читая каплю из хранилища Azure Blob:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Атрибут `QueueTrigger` говорит времени выполнения вызова функции всякий раз, `myqueue-items` когда сообщение очереди появляется в очереди. Атрибут `Blob` говорит времени выполнения использовать сообщение очереди, чтобы прочитать каплю в контейнере *для выборок.* Имя элемента капли в `samples-workitems` контейнере получено непосредственно из триггера очереди в качестве связывающего выражения ().`{queueTrigger}`

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Ручные триггеры

Чтобы запустить функцию вручную, используйте `NoAutomaticTrigger` атрибут, как показано здесь:

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

Процесс ручного запуска функции зависит от версии SDK.

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

Входные привязки предоставляют декларативный способ получения данных для кода из внешних служб или служб Azure. Выходные привязки обеспечивают способ обновления данных. В статье [Get started](webjobs-sdk-get-started.md) показан пример каждой из них.

Значение возврата метода для связывания вывода можно использовать, применяя атрибут к значению возврата метода. Смотрите пример в [значении возврата функции Azure.](../azure-functions/functions-bindings-return-value.md)

## <a name="binding-types"></a>Типы привязки

Процесс установки и управления типами связывания зависит от того, используете ли вы версию 3. *x* или версия 2. *x* SDK. Пакет можно установить для определенного типа связывания в разделе "Пакеты" [в справочной статье](#binding-reference-information)Azure Functions этого связывающего типа. Исключением является триггер и связывание файлов (для локальной файловой системы), которые не поддерживаются функциями Azure.

#### <a name="version-3x"></a>Версия 3. *x*

В версии 3. *x,* привязки хранилища `Microsoft.Azure.WebJobs.Extensions.Storage` включены в пакет. Вызов `AddAzureStorage` метод расширения `ConfigureWebJobs` в методе, как показано здесь:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод расширения `Add<binding>`, реализованный в расширении. Например, если вы хотите использовать привязку Azure `Microsoft.Azure.WebJobs.Extensions.CosmosDB` Cosmos DB, установите и позвоните, `AddCosmosDB`как это:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Чтобы использовать триггер таймера или привязку файлов, являющиеся частью основных служб, вызовите методы расширения `AddTimers` или `AddFiles` соответственно.

#### <a name="version-2x"></a>Версия 2. *x*

Эти триггерные и связывающие типы включены в версию 2. *x* `Microsoft.Azure.WebJobs` пакета:

* Хранилище BLOB-объектов
* Хранилище очередей
* Хранилище таблиц

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод `Use<binding>` для объекта `JobHostConfiguration`. Например, если вы хотите использовать триггер `Microsoft.Azure.WebJobs.Extensions` Таймер, установите и вызов `UseTimers` в методе, `Main` как показано здесь:

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

WebJobs позволяет привязать [`ExecutionContext`]к . С помощью этого привязки можно получить доступ в [`ExecutionContext`] качестве параметра в подписи функции. Например, следующий код использует объект контекста для доступа к идентификатору вызова, который вы можете использовать для корреляции всех журналов, созданных данным вызовом функции.  

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

Процесс привязки [`ExecutionContext`] к версии SDK зависит.

#### <a name="version-3x"></a>Версия 3. *x*

Вызов `AddExecutionContextBinding` метод расширения `ConfigureWebJobs` в методе, как показано здесь:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Версия 2. *x*

Пакет `Microsoft.Azure.WebJobs.Extensions`, упомянутый ранее, также предоставляет специальный тип привязки, который вы можете зарегистрировать, вызвав метод `UseCore`. Это связывание [`ExecutionContext`] позволяет определить параметр в подписи функции, которая включена следующим образом:

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

Можно настроить поведение некоторых триггеров и привязок. Процесс их настройки зависит от версии SDK.

* **Версия 3. *x*:** Установите `Add<Binding>` конфигурацию `ConfigureWebJobs`при вызове метода.
* **Версия 2. *x*:** Установите конфигурацию, установив свойства в `JobHost`объекте конфигурации, который вы проходите в.

Эти параметра, специфичных для связывания, эквивалентны настройкам в [файле проекта host.json](../azure-functions/functions-host-json.md) в Azure Functions.

Можно настроить следующие привязки:

* [Триггер Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Триггер Центров событий](#event-hubs-trigger-configuration-version-3x)
* [Триггер хранилища очередей](#queue-storage-trigger-configuration)
* [Привязка SendGrid](#sendgrid-binding-configuration-version-3x)
* [Триггер служебной шины](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Конфигурация триггера Azure CosmosDB (версия 3.* x*)

В этом примере показано, как настроить триггер Azure Cosmos DB:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Для получения более подробной информации смотрите обязательную статью [Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Концентраторы событий вызывают конфигурацию (версия 3.* x*)

В этом примере показано, как настроить триггер концентраторов событий:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Для получения более [подробной](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) информации см.

### <a name="queue-storage-trigger-configuration"></a>Конфигурация триггера хранения очереди

Эти примеры показывают, как настроить триггер хранения очереди:

#### <a name="version-3x"></a>Версия 3. *x*

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Для получения более [подробной](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) информации см.

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

Для получения более [подробной](../azure-functions/functions-host-json-v1.md#queues)информации см.

### <a name="sendgrid-binding-configuration-version-3x"></a>Конфигурация связывания SendGrid (версия 3.* x*)

В этом примере показано, как настроить связывание вывода SendGrid:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Для получения более [подробной](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) информации см.

### <a name="service-bus-trigger-configuration-version-3x"></a>Конфигурация триггера сервисного автобуса (версия 3.* x*)

В этом примере показано, как настроить триггер s-службы:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Для получения более [подробной](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) информации см.

### <a name="configuration-for-other-bindings"></a>Конфигурация для других привязок

Некоторые типы триггеров и связывания определяют свои собственные типы конфигураций. Например, триггер файла позволяет указать корневой путь для мониторинга, как в следующих примерах:

#### <a name="version-3x"></a>Версия 3. *x*

```cs
static async Task Main()
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
        await host.RunAsync();
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

Иногда требуется указать имя очереди, имя капли или контейнер, или имя стола в коде, а не жесткое кодирование. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для атрибута `QueueTrigger`.

Это можно сделать, `NameResolver` передав `JobHostConfiguration` объект объекту. В параметрах конструктора атрибута привязки и триггера нужно указать специальные заполнители, тогда код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей. Вы определяете заполнителей, окружая их процентами (%) знаки, как показано здесь:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Этот код позволяет использовать очередь с именем `logqueuetest` в тестовой среде и очередь с именем `logqueueprod` в производственной среде. Вместо фиксированного имени очереди требуется указать имя записи в коллекции `appSettings`.

Там в по `NameResolver` умолчанию, который вступает в силу, если вы не предоставляете пользовательский. Стандартное средство получает значения из настроек приложения или переменных среды.

Ваш `NameResolver` класс получает название `appSettings`очереди от , как показано здесь:

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

Вы настраиваете разрешитель с помощью инъекций зависимости. Для этих примеров необходим следующий оператор `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Вы добавляете разрешителя, [`ConfigureServices`] [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)вызывая метод расширения на, как в этом примере:

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

Передайте `NameResolver` свой класс `JobHost` объекту, как показано здесь:

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

Если вам нужно сделать некоторую работу в вашей функции, прежде чем использовать связывающий атрибут, `Queue`как `Blob`, или `Table`, вы можете использовать `IBinder` интерфейс.

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

Документация Azure Functions содержит справочную информацию о каждом типе связывания. Вы найдете следующую информацию в каждой обязательной справочной статье. (Этот пример основан на очереди хранения.)

* [Пакеты](../azure-functions/functions-bindings-storage-queue.md). Пакет, который необходимо установить, чтобы включить поддержку привязки в проект WebJobs SDK.
* [Примеры](../azure-functions/functions-bindings-storage-queue-trigger.md): Образцы кода. Пример библиотеки класса C' применяется к SDK WebJobs. Просто опустите `FunctionName` атрибут.
* [Атрибуты](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Атрибуты для использования для типа связывания.
* [Конфигурация](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Объяснения свойств атрибутов и параметров конструктора.
* [Использование](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Типы, к которые можно связываться, и информация о том, как работает привязка. Например: алгоритм опроса, обработка подозрительной очереди.
  
Список обязательных справочных статей см. в статье [Триггеров и привязок](../azure-functions/functions-triggers-bindings.md#supported-bindings) для Функций Azure. В этом списке привязки HTTP, Webhooks и Event Grid поддерживаются только функциями Azure, а не WebJobs SDK.

## <a name="disable-attribute"></a>Атрибут Disable 

Атрибут [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) позволяет контролировать, можно ли срабатывать функции. 

В следующем примере, если `Disable_TestJob` настройка `1` приложения `True` имеет значение или (случай нечувствительна), функция не будет работать. В этом случае среда выполнения создает сообщение журнала о том, что *функция Functions.TestJob отключена*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

При изменении значений настройки приложения на портале Azure WebJob перезапускается, чтобы подобрать новую настройку.

Атрибут может объявляться на уровне класса, метода или параметра. Имя параметра также может содержать выражения привязки.

## <a name="timeout-attribute"></a>Атрибут Timeout

Атрибут [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) приводит к отмене функции, если она не заканчивается в течение определенного периода времени. В следующем примере функция будет работать в течение одного дня без атрибута тайм-аута. Тайм-аут приводит к отмене функции через 15 секунд.

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

Вы можете применить атрибут Таймаута на уровне класса или метода, `JobHostConfiguration.FunctionTimeout`а также указать глобальный тайм-аут с помощью . Тайм-ауты уровня класса или метода переопределяют глобальные тайм-ауты.

## <a name="singleton-attribute"></a>Атрибут Singleton

Атрибут [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) гарантирует, что работает только один экземпляр функции, даже если есть несколько экземпляров веб-приложения хоста. Он делает это с помощью [распределенной блокировки.](#viewing-lease-blobs)

В этом примере в любой момент времени выполняется только один экземпляр `ProcessImage` функции:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

У некоторых триггеров есть встроенная поддержка для управления параллелизмом:

* **ОчередьТриггер**. Присвойте параметру `JobHostConfiguration.Queues.BatchSize` значение `1`.
* **ServiceBusTrigger**. Присвойте параметру `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` значение `1`.
* **FileTrigger**. Присвойте параметру `FileProcessor.MaxDegreeOfParallelism` значение `1`.

Эти параметры можно использовать для обеспечения отдельного выполнения функции в одном экземпляре. Чтобы убедиться, что только один экземпляр функции работает, когда веб-приложение масштабируется до нескольких экземпляров,`[Singleton(Mode = SingletonMode.Listener)]`примените блокировку синглтона уровня слушателя на функции (). Блокировки слушателя приобретаются при запуске JobHost. Если все три горизонтально масштабированные экземпляры запускаются одновременно, блокировки требует только один из экземпляров, а запускается только один прослушиватель.

### <a name="scope-values"></a>Значения области

Вы можете указать *выражение/значение области* на синглтоне. Выражение/значение гарантирует, что все выполнения функции в определенной области будут сериализованы. Внедрение более детальной блокировки таким образом может обеспечить определенный уровень параллелизма для вашей функции при сериализации других вызовов, как это продиктовано вашими требованиями. Например, в следующем коде выражение области `Region` связывается со значением входящего сообщения. Когда очередь содержит три сообщения в регионах Восток, Восток и Запад соответственно, сообщения, имеющие регион Восток, запускаются последовательно, в то время как сообщение с западным регионом происходит параллельно с сообщениями на Востоке.

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

Область по умолчанию `SingletonScope.Function`для блокировки означает область блокировки (путь аренды blob) привязана к полностью квалифицированной названию функции. Чтобы заблокировать функции, укажите `SingletonScope.Host` и используйте идентификатор области, которое одинаково во всех функциях, которые вы не хотите выполнять одновременно. В следующем примере выполняется только один экземпляр `AddItem` или `RemoveItem`:

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

Пакет SDK WebJobs использует аренды [больших двоичных объектов Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) для реализации распределенной блокировки. Арендные капли, используемые Синглтоном, `azure-webjobs-host` можно `AzureWebJobsStorage` найти в контейнере на складе под путями "замки". Например, путь к арендуемому большому двоичному объекту для первого примера `ProcessImage`, рассмотренного ранее, должен быть таким: `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Все пути включают идентификатор JobHost, в данном случае 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Асинхронные функции

Для получения информации о том, [Azure Functions documentation](../azure-functions/functions-dotnet-class-library.md#async)как кодировать функции async, см.

## <a name="cancellation-tokens"></a>Токены отмены

Информацию о том, как обрабатывать маркеры отмены, см. в документации по решению "Функции Azure" в разделе [Токены отмены](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Несколько экземпляров

Если ваше веб-приложение работает в нескольких экземплярах, на каждом экземпляре запускается непрерывный WebJob, который прослушивает триггеры и вызывает функции. Различные привязки триггера предназначены для эффективной совместной работы в экземплярах, чтобы масштабирование до большего количества экземпляров позволяло обрабатывать большую нагрузку.

В то время как некоторые триггеры могут привести к двойной обработке, триггеры хранения очередей и капли автоматически предотвращают обработку сообщения очереди или капли более одного раза. Для получения дополнительной информации в документации Azure Functions приведены сведения о [проектировании идентичных входных данных.](../azure-functions/functions-idempotent.md)

Триггер таймера автоматически гарантирует, что запускается только один экземпляр таймера, поэтому вы не получаете больше одного экземпляра функции, запущенного в заданное время.

Если вы хотите убедиться, что только один экземпляр функции выполняется даже при наличии [`Singleton`](#singleton-attribute) нескольких экземпляров веб-приложения хоста, вы можете использовать атрибут.

## <a name="filters"></a>Фильтры

Функциональные фильтры (предварительная версия) обеспечивают способ настройки конвейера выполнения WebJobs с помощью вашей собственной логики. Фильтры аналогичны [фильтрам ASP.NET Core.](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters) Их можно реализовать в качестве декларативных атрибутов, которые применяются к вашим функциям или классам. Дополнительные сведения см. на странице о [фильтрах функции](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

Мы рекомендуем систему лесозаготовок, разработанную для ASP.NET. В статье [Get started](webjobs-sdk-get-started.md) показано, как ее использовать. 

### <a name="log-filtering"></a>Фильтрация журналов

Каждый журнал, созданный экземпляром `ILogger`, имеет связанные параметры `Category` и `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)является перечислением, и код степлуказывает относительную важность:

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Сведения | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4 |
|Critical    | 5 |
|None        | 6 |

Вы можете самостоятельно фильтровать каждую категорию в определенную [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)категорию. Например, вы можете просмотреть все журналы для обработки триггера большого двоичного объекта, но только `Error` и выше для всего остального.

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* SDK опирается на фильтрацию, встроенную в ядро .NET. Класс `LogCategories` позволяет определить категории для конкретных функций, триггеров или пользователей. Он также определяет фильтры для определенных состояний хоста, как `Startup` и `Results`. Это позволяет точно настроить выход журнала. Если в определенных категориях не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

Добавьте в `LogCategories` следующий оператор:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Следующий пример строит фильтр, который по умолчанию фильтрует `Warning` все журналы на уровне. И `Function` `results` категории (эквивалент `Host.Results` в версии 2.* x*) отфильтровываются на `Error` уровне. Фильтр сравнивает текущую категорию со всеми зарегистрированными уровнями в экземпляре `LogCategories` и выбирает самое длинное совпадение. Это означает, `Debug` что `Host.Triggers` уровень, зарегистрированный на матчи `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

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

В версии 2. *x* SDK, вы используете `LogCategoryFilter` класс для управления фильтрацией. `LogCategoryFilter` Свойство `Default` с начальным `Information`значением, что означает, `Information` `Warning`что `Error`любые сообщения на , , или `Critical` уровни регистрируются, но любые сообщения на `Debug` или `Trace` уровнях отфильтровываются.

Как `LogCategories` и в версии 3. *x,* `CategoryLevels` свойство позволяет указывать уровни журнала для определенных категорий, чтобы можно было точно настроить выход журнала. Если в словаре `CategoryLevels` не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

В следующем примере создается фильтр, который по умолчанию фильтрует все журналы на уровне `Warning`. `Function` Категории `Host.Results` и категории `Error` фильтруются на уровне. Фильтр `LogCategoryFilter` сравнивает текущую категорию со всеми зарегистрированными уровнями `CategoryLevels` и выбирает самое длинное совпадение. Таким `Debug` образом, `Host.Triggers` уровень, зарегистрированный будет соответствовать `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

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

Процесс реализации пользовательской телеметрии для [Application Insights](../azure-monitor/app/app-insights-overview.md) зависит от версии SDK. Чтобы узнать, как настроить Application Insights, ознакомьтесь с разделом [Добавление журнала Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Версия 3. *x*

Потому что версия 3. *x* WebJobs SDK опирается на универсальный хост .NET Core, фабрика пользовательских телеметрии больше не предоставляется. Но можно добавить пользовательскую телеметрию в конвейер с помощью инъекций зависимости. Для примеров в этом разделе необходимы следующие операторы `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Следующая пользовательская [`ITelemetryInitializer`] реализация позволяет [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) добавить [`TelemetryConfiguration`]свой собственный по умолчанию.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Позвоните [`ConfigureServices`] в builder, [`ITelemetryInitializer`] чтобы добавить ваш пользовательский к конвейеру.

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

При [`TelemetryConfiguration`] построении все зарегистрированные [`ITelemetryInitializer`] типы включены. Чтобы узнать больше, смотрите [API Application Insights для пользовательских событий и метрик.](../azure-monitor/app/api-custom-events-metrics.md)

В версии 3. *x,* вам больше не [`TelemetryClient`] придется промывать, когда хост останавливается. Система впрыска зависимости .NET Core `ApplicationInsightsLoggerProvider`автоматически избавляется от зарегистрированного, который сбрасывает [`TelemetryClient`].

#### <a name="version-2x"></a>Версия 2. *x*

В версии 2. *x*, [`TelemetryClient`] созданный внутренне поставщиком Application Insights для WebJobs SDK использует. [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs) Когда конечная точка Application Insights недоступна или регулирует входящие запросы, этот канал [сохраняет запросы в файловой системе веб-приложения и позже повторно отправляет их](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Создается [`TelemetryClient`] классом, который `ITelemetryClientFactory`реализует. По умолчанию, [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)это .

Если вы хотите изменить какую-либо часть конвейера Application `ITelemetryClientFactory`Insights, вы можете предоставить [`TelemetryClient`]свой собственный, и услик будет использовать ваш класс для построения . Например, этот код `DefaultTelemetryClientFactory` переопределяет изменение `ServerTelemetryChannel`свойства:

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

Объект `SamplingPercentageEstimatorSettings` настраивает [адаптивную выборку.](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) Это означает, что в некоторых сценариях большого объема Приложения Insights отправляет на сервер выбранное подмножество телеметрических данных.

После создания фабрики телеметрии вы передаете ее поставщику журналов Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Дальнейшие действия

В этой статье приведены фрагменты кода, которые показывают, как обрабатывать общие сценарии для работы с WebJobs SDK. Полные примеры см. на странице с примерами [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetry Initializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[TelemetryConfiguration]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
