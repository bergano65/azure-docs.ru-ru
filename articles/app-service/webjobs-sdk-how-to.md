---
title: Использование пакета SDK WebJobs Azure
description: Ознакомьтесь со сведениями о написании кода для пакета SDK WebJobs. Создание заданий фоновой обработки, управляемых событиями, которые обращаются к данным в службах Azure и службах сторонних поставщиков.
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
ms.openlocfilehash: 67cd7f82597d306c8bf3c463d11457199aec7277
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815738"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Использование пакета SDK WebJobs Azure для фоновой обработки на основе событий

В этой статье приводятся рекомендации по работе с пакетом SDK для веб-заданий Azure. Чтобы сразу приступить к работе с веб-заданиями, см. статью Приступая к [работе с пакетом SDK для заданий Azure для обработки событий в фоновом режиме](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

Это ключевые различия между версией 3. *x* и версии 2. *x* пакета SDK веб-заданий:

* Версия 3. *x* добавляет поддержку .NET Core.
* В версии 3. *x*, необходимо явно установить расширение привязки хранилища, необходимое для пакета SDK веб-заданий. В версии 2. *x*, привязки к хранилищу были добавлены в пакет SDK.
* Инструментарий Visual Studio для .NET Core (3. *x*) отличается от инструментов для .NET Framework (2. *x*). Дополнительные сведения см. в статье [Разработка и развертывание веб-заданий с помощью Visual Studio — служба приложений Azure](webjobs-dotnet-deploy-vs.md).

По возможности для версии 3 предоставляются примеры. *x* и версии 2. *x*.

> [!NOTE]
> [Функции Azure](../azure-functions/functions-overview.md) основаны на пакете SDK для веб-заданий, и в этой статье содержатся ссылки на документацию по функциям Azure для некоторых разделов. Обратите внимание на следующие различия между функциями и пакетом SDK веб-заданий:
> * Функции Azure версии 2. *x* соответствует пакету SDK для веб-заданий версии 3. *x*и функции Azure 1. *x* соответствует пакету SDK для веб-заданий 2. *x*. В репозиториях исходного кода используется нумерация пакетов SDK для веб-заданий.
> * Пример кода для библиотек классов C# функций Azure подобен коду пакета SDK для веб-заданий, за исключением того, что в проекте пакета SDK веб-заданий не требуется атрибут `FunctionName`.
> * Некоторые типы привязок поддерживаются только в функциях, таких как HTTP (веб-перехватчики) и сетка событий (основанная на HTTP).
>
> Дополнительные сведения см. в разделе [Сравнение функций и веб-заданий](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Узел веб-заданий

Узел — это контейнер среды выполнения для функций.  Он прослушивает триггеры и вызовы функций. В версии 3. *x*, узел является реализацией `IHost`. В версии 2. *x*используется объект `JobHost`. Вам нужно создать экземпляр узла в коде и написать код, чтобы настроить его работу.

Это основное различие между использованием пакета SDK для веб-заданий и его непрямое использование с помощью функций Azure. В функциях Azure служба управляет узлом, и вы не можете настроить узел, написав код. Функции Azure позволяют настроить поведение узла с помощью параметров в файле host. JSON. Эти параметры являются строками, а не кодом, и это ограничивает виды настроек, которые можно выполнять.

### <a name="host-connection-strings"></a>Строки подключения узла

Пакет SDK веб-заданий ищет строки подключения службы хранилища Azure и служебной шины Azure в файле Local. Settings. JSON при локальном запуске или в среде веб-задания при запуске в Azure. По умолчанию параметр строки подключения к хранилищу с именем `AzureWebJobsStorage` является обязательным.  

Версия 2. *x* пакета SDK позволяет использовать собственные имена для этих строк подключения или сохранять их в других местах. Вы можете задать имена в коде с помощью [`JobHostConfiguration`], как показано ниже:

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

Из-за версии 3. *x* использует API конфигурации .NET Core по умолчанию, интерфейс API для изменения имен строк подключения отсутствует.

### <a name="host-development-settings"></a>Параметры разработки узла

Вы можете запустить узел в режиме разработки, чтобы повысить эффективность локальной разработки. Ниже приведены некоторые параметры, которые изменяются при запуске в режиме разработки.

| Свойство | Параметр разработки |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` — чтобы увеличить выходные данные журнала. |
| `Queues.MaxPollingInterval`  | Низкое значение для обеспечения немедленного запуска методов очереди.  |
| `Singleton.ListenerLockPeriod` | 15 секунд для ускорения последовательной разработки. |

Процесс включения режима разработки зависит от версии пакета SDK. 

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* использует стандартные ASP.NET Core API. Вызовите метод [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) для экземпляра [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Передайте строку с именем `development`, как показано в следующем примере:

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

В классе `JobHostConfiguration` имеется метод `UseDevelopmentSettings`, который включает режим разработки.  В следующем примере показано, как использовать параметры разработки. Чтобы `config.IsDevelopment` возвращали `true` при локальном запуске, установите локальную переменную среды с именем `AzureWebJobsEnv` со значением `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Управление параллельными подключениями (версия 2). *x*)

В версии 3. *x*, по умолчанию предельное число подключений равно бесконечному. Если по некоторым причинам необходимо изменить это ограничение, можно использовать свойство [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) класса [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) .

В версии 2. *x*, вы управляете количеством одновременных подключений к узлу с помощью API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . В 2. *x*, следует увеличить это значение по умолчанию, равное 2, перед запуском узла веб-заданий.

Все исходящие HTTP-запросы, выполненные из функции с помощью `HttpClient`ного потока через `ServicePointManager`. После достижения значения, установленного в `DefaultConnectionLimit`, `ServicePointManager` начинает ставить запросы в очередь перед их отправкой. Предположим, что для ограничения `DefaultConnectionLimit` установлено значение 2 и код включает 1000 HTTP-запросов. Первоначально в ОС разрешено только два запроса. Остальные 998 поставлены в очередь, пока не появится место для них. Это означает, что `HttpClient` может исключаться из-за истечения времени ожидания, так как он по-видимому сделал запрос, но запрос никогда не отправлялся операционной системой на целевой сервер. Таким образом, вы можете столкнуться с поведением, которое не имеет смысла: локальному клиенту `HttpClient` потребуется 10 секунд, чтобы выполнить запрос, но служба будет возвращать каждый запрос за 200 мс. 

Значение по умолчанию для приложений ASP.NET — `Int32.MaxValue`и, скорее всего, будет хорошо работать для веб-заданий, работающих в базовом или более высоком плане службы приложений. Веб-заданиям обычно требуется параметр Always On и они поддерживаются только планами службы приложений "базовый" и "более высокий".

Если ваше задание WebJob работает в бесплатном или общем плане службы приложений, приложение ограничено песочницей службы приложений, которая в настоящее время имеет такое [ограничение подключений — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). При несвязанном ограничении подключений в `ServicePointManager`более вероятно, что будет достигнуто пороговое значение подключения "песочницы", и сайт завершит работу. В этом случае установка более низкого значения параметра `DefaultConnectionLimit`, например 50 или 100, может не допустить события выше и обеспечить достаточную пропускную способность.

Параметр нужно настроить перед выполнением HTTP-запросов. По этой причине узел веб-заданий не должен настраивать параметр автоматически. Могут быть HTTP-запросы, происходящие перед запуском узла, что может привести к непредвиденному поведению. Лучшим подходом является установка значения непосредственно в методе `Main` перед инициализацией `JobHost`, как показано ниже:

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

Функции должны быть открытыми методами и должны иметь один атрибут триггера или атрибут [`NoAutomaticTrigger`](#manual-triggers) .

### <a name="automatic-triggers"></a>Автоматические триггеры

Автоматические триггеры вызывают функцию в ответ на событие. Рассмотрим этот пример функции, которая активируется сообщением, добавленным в хранилище очередей Azure. Он реагирует на считывание большого двоичного объекта из хранилища BLOB-объектов Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Атрибут `QueueTrigger` указывает среде выполнения вызывать функцию всякий раз, когда в очереди `myqueue-items` появляется сообщение очереди. Атрибут `Blob` указывает среде выполнения использовать сообщение очереди для чтения большого двоичного объекта в контейнере *Sample-WorkItems* . Содержимое сообщения очереди, переданное функции в параметре `myQueueItem`, является именем большого двоичного объекта.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Ручные триггеры

Чтобы запустить функцию вручную, используйте атрибут `NoAutomaticTrigger`, как показано ниже:

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

Процесс запуска функции вручную зависит от версии пакета SDK.

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

Входные привязки предоставляют декларативный способ получения данных для кода из внешних служб или служб Azure. Выходные привязки обеспечивают способ обновления данных. В статье Начало [работы](webjobs-sdk-get-started.md) показан пример каждой из них.

Можно использовать возвращаемое значение метода для выходной привязки, применяя атрибут к возвращаемому значению метода. См. пример в разделе [Использование возвращаемого значения функции Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Типы привязки

Процесс установки и управления типами привязок зависит от того, используете ли вы версию 3. *x* или версии 2. *x* пакета SDK. Пакет, который необходимо установить для конкретного типа привязки, можно найти в разделе "пакеты" [справочной статьи](#binding-reference-information)о функциях Azure для этого типа привязки. Исключением являются триггеры и привязка файлов (для локальной файловой системы), которые не поддерживаются функциями Azure.

#### <a name="version-3x"></a>Версия 3. *x*

В версии 3. *x*, привязки к хранилищу включены в пакет `Microsoft.Azure.WebJobs.Extensions.Storage`. Вызовите метод расширения `AddAzureStorage` в методе `ConfigureWebJobs`, как показано ниже:

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

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод расширения `Add<binding>`, реализованный в расширении. Например, если вы хотите использовать привязку Azure Cosmos DB, установите `Microsoft.Azure.WebJobs.Extensions.CosmosDB` и вызовите `AddCosmosDB`следующим образом:

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

Эти типы триггеров и привязок включены в версию 2. *x* пакета `Microsoft.Azure.WebJobs`:

* Хранилище BLOB-объектов
* Хранилище очередей
* Хранилище таблиц

Чтобы использовать другие типы триггеров и привязок, установите пакет NuGet, который их содержит, и вызовите метод `Use<binding>` для объекта `JobHostConfiguration`. Например, если вы хотите использовать триггер таймера, установите `Microsoft.Azure.WebJobs.Extensions` и вызовите `UseTimers` в методе `Main`, как показано ниже:

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

Процесс привязки к [`ExecutionContext`] зависит от версии пакета SDK.

#### <a name="version-3x"></a>Версия 3. *x*

Вызовите метод расширения `AddExecutionContextBinding` в методе `ConfigureWebJobs`, как показано ниже:

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

Пакет `Microsoft.Azure.WebJobs.Extensions`, упомянутый ранее, также предоставляет специальный тип привязки, который вы можете зарегистрировать, вызвав метод `UseCore`. Эта привязка позволяет определить [`ExecutionContext`] параметр в сигнатуре функции, которая включается следующим образом:

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

Можно настроить поведение некоторых триггеров и привязок. Процесс настройки зависит от версии пакета SDK.

* **Версия 3. *x*:** Задайте конфигурацию при вызове метода `Add<Binding>` в `ConfigureWebJobs`.
* **Версия 2. *x*:** Задайте конфигурацию, задав свойства в объекте конфигурации, который передается `JobHost`.

Эти зависящие от привязки параметры эквивалентны параметрам в [файле проекта Host. JSON](../azure-functions/functions-host-json.md) в функциях Azure.

Можно настроить следующие привязки:

* [Триггер CosmosDB для Azure](#azure-cosmosdb-trigger-configuration-version-3x)
* [Триггер концентраторов событий](#event-hubs-trigger-configuration-version-3x)
* [Триггер хранилища очередей](#queue-storage-trigger-configuration)
* [Привязка SendGrid](#sendgrid-binding-configuration-version-3x)
* [Триггер служебной шины](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Конфигурация триггера Azure CosmosDB (версия 3). *x*)

В этом примере показано, как настроить триггер Azure Cosmos DB.

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

Дополнительные сведения см. в статье о [привязке CosmosDB для Azure](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Конфигурация триггеров концентраторов событий (версия 3). *x*)

В этом примере показано, как настроить триггер концентраторов событий.

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

Дополнительные сведения см. в статье [Привязка концентраторов событий](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) .

### <a name="queue-storage-trigger-configuration"></a>Настройка триггера хранилища очередей

В этих примерах показано, как настроить триггер хранилища очередей.

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

Дополнительные сведения см. в статье [Привязка хранилища очередей](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) .

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

Дополнительные сведения см. в [справочнике по Host. JSON v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Конфигурация привязки SendGrid (версия 3). *x*)

В этом примере показано, как настроить выходную привязку SendGrid:

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

Дополнительные сведения см. в статье о [привязке SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Конфигурация триггера служебной шины (версия 3). *x*)

В этом примере показано, как настроить триггер служебной шины.

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

Дополнительные сведения см. в статье [Привязка служебной шины](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Конфигурация для других привязок

Некоторые типы триггеров и привязок определяют собственные пользовательские типы конфигурации. Например, файловый триггер позволяет указать корневой путь для отслеживания, как показано в следующих примерах:

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

Иногда требуется указать имя очереди, имя большого двоичного объекта или контейнера или имя таблицы в коде, а не жестко закодировать его. Например, в некоторых случаях в файле конфигурации или в переменной среды необходимо указывать имя очереди для атрибута `QueueTrigger`.

Это можно сделать, передав объект `NameResolver` в объект `JobHostConfiguration`. В параметрах конструктора атрибута привязки и триггера нужно указать специальные заполнители, тогда код `NameResolver` будет указывать фактические значения, которые следует использовать вместо этих заполнителей. Вы определяете заполнители, окружающие их с помощью процента (%) символы, как показано ниже:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Этот код позволяет использовать очередь с именем `logqueuetest` в тестовой среде и очередь с именем `logqueueprod` в производственной среде. Вместо фиксированного имени очереди требуется указать имя записи в коллекции `appSettings`.

Существует `NameResolver` по умолчанию, которая вступает в силу, если не указать пользовательскую. Стандартное средство получает значения из настроек приложения или переменных среды.

Класс `NameResolver` получает имя очереди из `appSettings`, как показано ниже:

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

Вы настраиваете сопоставитель с помощью внедрения зависимостей. Для этих примеров необходим следующий оператор `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Сопоставитель добавляется путем вызова метода расширения [`ConfigureServices`] для [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), как показано в следующем примере:

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

Передайте класс `NameResolver` в объект `JobHost`, как показано ниже:

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

Если необходимо выполнить некоторую работу в функции, прежде чем использовать атрибут привязки, например `Queue`, `Blob`или `Table`, можно использовать интерфейс `IBinder`.

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

В документации по функциям Azure содержатся справочные сведения о каждом типе привязки. В каждой справочной статье привязок содержатся следующие сведения. (Этот пример основан на очереди хранилища.)

* [Пакеты](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Пакет, который необходимо установить, включает поддержку привязки в проекте пакета SDK веб-заданий.
* [Примеры](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Примеры кода. Пример C# библиотеки классов применяется к пакету SDK для веб-заданий. Просто опустите атрибут `FunctionName`.
* [Атрибуты](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Атрибуты, используемые для типа привязки.
* [Конфигурация](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Пояснения к свойствам атрибута и параметрам конструктора.
* [Использование](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Типы, к которым можно выполнить привязку, и сведения о принципах работы привязки. Например: алгоритм опроса, обработка подозрительной очереди.
  
Список справочных статей по привязке см. в разделе "поддерживаемые привязки" статьи [триггеры и привязки](../azure-functions/functions-triggers-bindings.md#supported-bindings) для функций Azure. В этом списке привязки HTTP, веб-перехватчиков и службы "Сетка событий" поддерживаются только функциями Azure, а не пакетом SDK веб-заданий.

## <a name="disable-attribute"></a>Атрибут Disable 

Атрибут [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) позволяет управлять возможностью запуска функции. 

В следующем примере, если параметр приложения `Disable_TestJob` имеет значение `1` или `True` (без учета регистра), функция не будет выполняться. В этом случае среда выполнения создает сообщение журнала о том, что *функция Functions.TestJob отключена*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

При изменении значений параметров приложения в портал Azure веб-задание перезапускается для выбора нового параметра.

Атрибут может объявляться на уровне класса, метода или параметра. Имя параметра также может содержать выражения привязки.

## <a name="timeout-attribute"></a>Атрибут Timeout

Атрибут [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) вызывает отмену функции, если она не завершается в течение заданного промежутка времени. В следующем примере функция будет выполняться в течение одного дня без атрибута timeout. Время ожидания приводит к отмене функции через 15 секунд.

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

Можно применить атрибут timeout на уровне класса или метода, а также задать глобальное время ожидания с помощью `JobHostConfiguration.FunctionTimeout`. Время ожидания на уровне класса или метода переопределяет глобальные времена ожидания.

## <a name="singleton-attribute"></a>Атрибут Singleton

Атрибут [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) гарантирует, что выполняется только один экземпляр функции, даже если имеется несколько экземпляров ведущего веб-приложения. Для этого используется [Распределенная блокировка](#viewing-lease-blobs).

В этом примере в любое заданное время выполняется только один экземпляр функции `ProcessImage`:

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
* **Филетригжер**. Задайте для параметра `FileProcessor.MaxDegreeOfParallelism` значение `1`.

Эти параметры можно использовать для обеспечения отдельного выполнения функции в одном экземпляре. Чтобы обеспечить выполнение только одного экземпляра функции при масштабировании веб-приложения до нескольких экземпляров, примените одноэлементную блокировку на уровне прослушивателя для функции (`[Singleton(Mode = SingletonMode.Listener)]`). Блокировка прослушивателя запрашивается при запуске JobHost. Если все три горизонтально масштабированные экземпляры запускаются одновременно, блокировки требует только один из экземпляров, а запускается только один прослушиватель.

### <a name="scope-values"></a>Значения области

Можно указать *выражение или значение области действия* для Singleton. Выражение/значение гарантирует, что все выполнения функции в определенной области будут сериализованы. Реализация более детализированной блокировки таким образом может обеспечить определенный уровень параллелизма для вашей функции при сериализации других вызовов в соответствии с вашими требованиями. Например, в следующем коде выражение SCOPE привязывается к `Region`ому значению входящего сообщения. Если очередь содержит три сообщения в регионах "Восток", "Восток" и "Западная часть" соответственно, сообщения с регионом "Восточная часть" выполняются последовательно, тогда как сообщение с регионом "Запад" выполняется параллельно с ними в восточной части.

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

Областью блокировки по умолчанию является `SingletonScope.Function`, что означает, что область блокировки (путь аренды BLOB-объекта) привязана к полному имени функции. Чтобы заблокировать функции, укажите `SingletonScope.Host` и используйте имя идентификатора области, которое будет одинаковым для всех функций, которые не нужно запускать одновременно. В следующем примере выполняется только один экземпляр `AddItem` или `RemoveItem`:

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

Пакет SDK WebJobs использует аренды [больших двоичных объектов Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) для реализации распределенной блокировки. Большие двоичные объекты аренды, используемые Singleton, можно найти в контейнере `azure-webjobs-host` в учетной записи хранения `AzureWebJobsStorage` по пути "Locks". Например, путь к арендуемому большому двоичному объекту для первого примера `ProcessImage`, рассмотренного ранее, должен быть таким: `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Все пути включают идентификатор JobHost, в данном случае 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Асинхронные функции

Сведения о том, как кодировать асинхронные функции, см. в [документации по функциям Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Токены отмены

Информацию о том, как обрабатывать маркеры отмены, см. в документации по решению "Функции Azure" в разделе [Токены отмены](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Выполнение на нескольких экземплярах

Если ваше веб-приложение работает в нескольких экземплярах, на каждом экземпляре запускается непрерывный WebJob, который прослушивает триггеры и вызывает функции. Различные привязки триггера предназначены для эффективной совместной работы в экземплярах, чтобы масштабирование до большего количества экземпляров позволяло обрабатывать большую нагрузку.

Хотя некоторые триггеры могут привести к тому, что триггеры хранилища с двойной обработкой, очередью и хранилищем BLOB-объектов автоматически не позволяют функции обрабатывать сообщения очереди или большой двоичный объект более одного раза. Дополнительные сведения см. в разделе [Разработка для идентичных входных данных](../azure-functions/functions-idempotent.md) в документации по функциям Azure.

Триггер таймера автоматически гарантирует, что запускается только один экземпляр таймера, поэтому вы не получаете больше одного экземпляра функции, запущенного в заданное время.

Если необходимо обеспечить выполнение только одного экземпляра функции даже при наличии нескольких экземпляров ведущего веб-приложения, можно использовать атрибут [`Singleton`](#singleton-attribute) .

## <a name="filters"></a>Фильтры

Функциональные фильтры (предварительная версия) обеспечивают способ настройки конвейера выполнения WebJobs с помощью вашей собственной логики. Фильтры похожи на [фильтры ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Их можно реализовать как декларативные атрибуты, применяемые к функциям или классам. Дополнительные сведения см. на странице о [фильтрах функции](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

Мы рекомендуем использовать платформу ведения журналов, разработанную для ASP.NET. В статье [Начало работы](webjobs-sdk-get-started.md) показано, как использовать ее. 

### <a name="log-filtering"></a>Фильтрация журнала

Каждый журнал, созданный экземпляром `ILogger`, имеет связанные параметры `Category` и `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) является перечислением, а целочисленный код указывает на относительную важность:

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4\. |
|критические ошибки.    | 5 |
|Нет        | 6 |

Каждую категорию можно независимо отфильтровать с определенным [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Например, вы можете просмотреть все журналы для обработки триггера большого двоичного объекта, но только `Error` и выше для всего остального.

#### <a name="version-3x"></a>Версия 3. *x*

Версия 3. *x* пакета SDK полагается на фильтрацию, встроенную в .NET Core. Класс `LogCategories` позволяет определить категории для конкретных функций, триггеров или пользователей. Он также определяет фильтры для конкретных состояний узла, например `Startup` и `Results`. Это позволяет точно настроить вывод журнала. Если в определенных категориях не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

Добавьте в `LogCategories` следующий оператор:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

В следующем примере создается фильтр, по умолчанию фильтрующий все журналы на уровне `Warning`. Категории `Function` и `results` (эквивалентны `Host.Results` в версии 2. *x*) фильтруется на уровне `Error`. Фильтр сравнивает текущую категорию со всеми зарегистрированными уровнями в экземпляре `LogCategories` и выбирает самое длинное совпадение. Это означает, что `Debug` уровень, зарегистрированный для `Host.Triggers`, соответствует `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

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

В версии 2. *x* пакета SDK для управления фильтрацией используется класс `LogCategoryFilter`. `LogCategoryFilter` имеет свойство `Default` с начальным значением `Information`, что означает, что все сообщения на уровнях `Information`, `Warning`, `Error`или `Critical` записываются в журнал, но все сообщения на уровнях `Debug` или `Trace` отфильтровываются.

Как и в случае с `LogCategories` в версии 3. *x*, свойство `CategoryLevels` позволяет указать уровни ведения журнала для конкретных категорий, чтобы можно было точно настроить вывод журнала. Если в словаре `CategoryLevels` не найдено совпадение, фильтр возвращается к значению `Default` при принятии решения о фильтрации сообщения.

В следующем примере создается фильтр, который по умолчанию фильтрует все журналы на уровне `Warning`. Категории `Function` и `Host.Results` фильтруются на уровне `Error`. Фильтр `LogCategoryFilter` сравнивает текущую категорию со всеми зарегистрированными уровнями `CategoryLevels` и выбирает самое длинное совпадение. Таким образом, уровень `Debug`, зарегистрированный для `Host.Triggers`, будет соответствовать `Host.Triggers.Queue` или `Host.Triggers.Blob`. Это позволит вам управлять более широкими категориями, не добавляя их.

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

Из-за версии 3. *x* пакета SDK для веб-заданий основывается на универсальном узле .NET Core, настраиваемая фабрика данных телеметрии больше не предоставляется. Но вы можете добавить пользовательскую телеметрию в конвейер с помощью внедрения зависимостей. Для примеров в этом разделе необходимы следующие операторы `using`:

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

Когда [`TelemetryConfiguration`] создана, все зарегистрированные типы [`ITelemetryInitializer`] включены. Дополнительные сведения см. в статье [Application Insights API для пользовательских событий и метрик](../azure-monitor/app/api-custom-events-metrics.md).

В версии 3. *x*, вам больше не нужно очищать [`TelemetryClient`] при остановке узла. Система внедрения зависимостей .NET Core автоматически удаляет зарегистрированный `ApplicationInsightsLoggerProvider`, который очищает [`TelemetryClient`].

#### <a name="version-2x"></a>Версия 2. *x*

В версии 2. *x*, [`TelemetryClient`] , созданный внутренне поставщиком Application Insights для пакета SDK веб-заданий, использует [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Когда конечная точка Application Insights недоступна или регулирует входящие запросы, этот канал [сохраняет запросы в файловой системе веб-приложения и позже повторно отправляет их](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

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

Объект `SamplingPercentageEstimatorSettings` настраивает [адаптивную выборку](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Это означает, что в некоторых сценариях с большим объемом работы Application Insights отправляет на сервер выбранное подмножество данных телеметрии.

После создания фабрики телеметрии ее необходимо передать в регистратор Application Insights.

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Дальнейшие действия

В этой статье приведены фрагменты кода, демонстрирующие способы решения распространенных сценариев работы с пакетом SDK для веб-заданий. Полные примеры см. на странице с примерами [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[TelemetryConfiguration]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
["Жобхостконфигуратион"]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
