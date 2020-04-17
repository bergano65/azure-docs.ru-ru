---
title: Приложения Исследования приложений для обслуживания работников (приложения, не входящих в HTTP)
description: Мониторинг приложений .NET Core/.NET Framework non-HTTP с помощью приложений Azure Monitor.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536732"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Анализ приложений для приложений для работы (не-HTTP приложений)

Application Insights выпускает новый SDK, который `Microsoft.ApplicationInsights.WorkerService`лучше всего подходит для не-HTTP рабочих нагрузок, таких как обмен сообщениями, фоновые задачи, консольные приложения и т.д. Эти типы приложений не имеют понятия о входящих запросhttp, как традиционные ASP.NET/ASP.NET Основные Web-приложения, и, следовательно, использование пакетов Application Insights для [ASP.NET](asp-net.md) или [ASP.NET основных](asp-net-core.md) приложений не поддерживается.

Новый SDK не делает никакой телеметрической коллекции сам по себе. Вместо этого, он приносит в других известных приложений Исследования авто коллекционеров, как [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) и т.д. Этот SDK предоставляет методы `IServiceCollection` расширения, чтобы включить и настроить телеметрию сбора.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

[Приложение Insights SDK для службы для работников](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) лучше всего подходит для не-HTTP приложений независимо от того, где и как они работают. Если приложение работает и имеет подключение к Azure, можно собрать телеметрию. Мониторинг Application Insights поддерживается везде .NET Core. Этот пакет можно использовать в недавно введенной [службе работников .NET Core 3.0,](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [фоновых задачах в Asp.Net Core 2.1/2.2,](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)консольных приложениях (.NET Core/ .NET Framework) и т.д.

## <a name="prerequisites"></a>Предварительные требования

Действительный ключ для анализа приложений. Этот ключ необходим для отправки любой телеметрии в Application Insights. Если вам нужно создать новый ресурс Application Insights для получения ключа приборов, [см.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="using-application-insights-sdk-for-worker-services"></a>Использование SDK-приложений Для служб для работников

1. Установите в приложение пакет [Microsoft.ApplicationInsights.WorkerService.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)
   Следующий фрагмент показывает изменения, которые необходимо добавить в `.csproj` файл проекта.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Метод `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` расширения `IServiceCollection`вызова на, обеспечивая ключ приборов. Этот метод следует вызывать в начале приложения. Точное местоположение зависит от типа приложения.

1. Извлеките `ILogger` экземпляр `TelemetryClient` или экземпляр из контейнера «Инъекция зависимости» (DI) путем вызова `serviceProvider.GetRequiredService<TelemetryClient>();` или использования впрыска конструктора. Этот шаг вызовет настройку модулей и автоматического `TelemetryConfiguration` сбора.

Конкретные инструкции для каждого типа приложения описаны в следующих разделах.

## <a name="net-core-30-worker-service-application"></a>приложение обслуживания работников .NET Core 3.0

Полный пример приведен в общих [примерах здесь](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Скачать и установить [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Создание нового проекта службы работы либо с помощью нового шаблона проекта Visual Studio, либо с командной строкой`dotnet new worker`
3. Установите в приложение пакет [Microsoft.ApplicationInsights.WorkerService.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)

4. Добавьте `services.AddApplicationInsightsTelemetryWorkerService();` `CreateHostBuilder()` к методу в классе, `Program.cs` как в этом примере:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Измените `Worker.cs` ваш в качестве приведенного ниже примера.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Настройка ключа приборов.

    Хотя вы можете предоставить ключ приборов в качестве `AddApplicationInsightsTelemetryWorkerService`аргумента, мы рекомендуем вам указать ключ приборов в конфигурации. Следующий пример кода показывает, как указать ключ приборов в `appsettings.json`. Убедитесь, что `appsettings.json` во время публикации его скопировали в корневую папку приложения.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Кроме того, укажите ключ приборов в любой из следующих переменных среды.
`APPINSIGHTS_INSTRUMENTATIONKEY` или `ApplicationInsights:InstrumentationKey`

Например: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Или`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Как `APPINSIGHTS_INSTRUMENTATIONKEY` правило, определяется ключ приборов для приложений, развернутых в Web-приложениях, как Web Jobs.

> [!NOTE]
> Ключ приборов, указанный в коде, побеждает переменную `APPINSIGHTS_INSTRUMENTATIONKEY`среды, которая побеждает другие опции.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET основные фоновые задачи с размещенными службами

[В этом](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) документе описывается, как создавать задачи фона в ASP.NET приложении Core 2.1/2.2.

Полный пример приведен в общих [примерах здесь](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Установите пакет Microsoft.ApplicationInsights.WorkerService (пакетhttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.
2. Добавьте `services.AddApplicationInsightsTelemetryWorkerService();` `ConfigureServices()` к методу, как в этом примере:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Ниже приводится `TimedHostedService` код, для котором находится логика фоновой задачи.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Настройка ключа приборов.
   Используйте `appsettings.json` то же самое из приведенного выше примера службы работников .NET Core 3.0.

## <a name="net-corenet-framework-console-application"></a>Приложение рамочной консоли .NET Core/.NET

Как уже упоминалось в начале этой статьи, новый пакет может быть использован для включения приложения Insights Telemetry даже от обычного приложения консоли. Этот пакет [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)цели, и, следовательно, могут быть использованы для консольных приложений в .NET Core 2.0 или выше, и .NET Framework 4.7.2 или выше.

Полный пример приведен в общих [примерах здесь](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Установите пакет Microsoft.ApplicationInsights.WorkerService (пакетhttps://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.

2. Изменение Program.cs в качестве приведенного ниже примера.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Это приложение консоли также `TelemetryConfiguration`использует тот же по умолчанию, и он может быть настроен таким же образом, как примеры в предыдущем разделе.

## <a name="run-your-application"></a>Запуск приложения

Запустите приложение. Пример работников из всех вышеперечисленных делает http вызова каждую секунду, чтобы bing.com, а также испускает несколько журналов с помощью ILogger. Эти линии `StartOperation` обернуты внутри `TelemetryClient`вызова, который используется `RequestTelemetry` для создания операции (в этом примере называется "операция"). Application Insights будет собирать эти журналы ILogger (предупреждение или выше по умолчанию) `RequestTelemetry` и зависимостей, и они будут соотнесены с отношениями между родителями и детьми. Корреляция также работает поперечной границы процесса/сети. Например, если вызов был сделан другому контролируемому компоненту, то он будет также соотнесен с этим родительским компонентом.

Эта пользовательская `RequestTelemetry` операция может рассматриваться как эквивалент входящего веб-запроса в типичном веб-приложении. Хотя нет необходимости использовать операцию, она лучше всего вписывается в `RequestTelemetry` [модель корреляционных данных Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - с участием родительской операции, и каждая телеметрия, генерируемая внутри итерации работника, рассматривается как логически относящаяся к той же операции. Этот подход также гарантирует, что все телеметрии генерируется `operation_id`(автоматический и ручной) будет иметь то же самое . Поскольку выборка `operation_id`основана на, алгоритм выборки либо сохраняет, либо отбрасывает всю телеметрию из одной итерации.

Ниже перечислены полные телеметрии автоматически собраны приложения Исследования.

### <a name="live-metrics"></a>Динамические метрики

[Метрики в реальном времени](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) могут быть использованы для быстрого проверки правильного мониторинга Application Insights. Хотя это может занять несколько минут, прежде чем телеметрия начинает появляться в портале и аналитики, Live Metrics покажет использование процессора процесса работы в почти в режиме реального времени. Он также может отображать другие телеметрии, как запросы, зависимости, следы и т.д.

### <a name="ilogger-logs"></a>Логи ILogger

Входы, излучаемые с помощью `ILogger` тяжести `Warning` или больше, автоматически фиксируются. Следуйте [iLogger документы,](ilogger.md#control-logging-level) чтобы настроить, какие уровни журнала захвачены Application Insights.

### <a name="dependencies"></a>Зависимости

Коллекция зависимостей включена по умолчанию. [В этой](asp-net-dependencies.md#automatically-tracked-dependencies) статье разъясняются автоматически собранные зависимости, а также содержатся шаги для ручного отслеживания.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`включен по умолчанию, и он будет собирать набор счетчиков по умолчанию из приложений .NET Core 3.0. В учебнике [EventCounter](eventcounters.md) перечислен набор собранных счетчиков по умолчанию. Он также имеет инструкции по настройке списка.

### <a name="manually-tracking-additional-telemetry"></a>Ручное отслеживание дополнительной телеметрии

В то время как SDK автоматически собирает телеметрию, как это объясняется выше, в большинстве случаев пользователю придется отправить дополнительную телеметрию в службу Application Insights. Рекомендуемый способ отслеживания дополнительной телеметрии — `TelemetryClient` получение экземпляра от инъекций зависимости, а затем вызов одного из поддерживаемых `TrackXXX()` методов [API](api-custom-events-metrics.md) на нем. Другим типичным примером использования является [пользовательское отслеживание операций.](custom-operations-tracking.md) Этот подход демонстрируется в приведенных выше примерах «Рабочий».

## <a name="configure-the-application-insights-sdk"></a>Настройка приложения Исследования SDK

По `TelemetryConfiguration` умолчанию, используемый службой работы SDK, аналогичен автоматической конфигурации, используемой в приложении ASP.NET `HttpContext`или ASP.NET Core, за вычетом телеметрических инициаторов, используемых для обогащения телеметрии.

Можно настроить SDK Application Insights для службы работников для изменения конфигурации по умолчанию. Пользователи Application Insights ASP.NET Core SDK могут быть знакомы с изменением конфигурации с помощью [встроенной инъекции ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)Core. СДК «WorkerService» также основана на аналогичных принципах. Внести почти все `ConfigureServices()` изменения конфигурации в `IServiceCollection`разделе, позвонив соответствующие методы на , как описано ниже.

> [!NOTE]
> При использовании этого SDK изменение `TelemetryConfiguration.Active` конфигурации путем изменения не поддерживается, и изменения не будут отражены.

### <a name="using-applicationinsightsserviceoptions"></a>Использование ApplicationApplicationInsightsServiceOptions

Вы можете изменить несколько общих параметров, перейдя `ApplicationInsightsServiceOptions` к, `AddApplicationInsightsTelemetryWorkerService`как в этом примере:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Обратите `ApplicationInsightsServiceOptions` внимание, что в этом SDK находится в пространстве `Microsoft.ApplicationInsights.WorkerService` имен, `Microsoft.ApplicationInsights.AspNetCore.Extensions` в отличие от в ASP.NET Core SDK.

Обычно используемые настройки в`ApplicationInsightsServiceOptions`

|Параметр | Описание | По умолчанию
|---------------|-------|-------
|ВключитьКвипулпульсМетрический поток | Функция включить/отключить LiveMetrics | Да
|ВключитьАдаптивнуювыборную выборку | Включить/отключить адаптивную выборку | Да
|ВключитьСердцебить | Функция включить/отключить сердцебиение, которая периодически (15-минутный по умолчанию) отправляет пользовательский метрику под названием "HeartBeatState" с информацией о времени выполнения, как .NET Version, информация об окружающей среде Azure, если это применимо, и т.д. | Да
|AddAutoCollectedМетрический экстрактор | Включить / отключить экстрактор AutoCollectedMetrics, который является TelemetryProcessor, который посылает предварительно агрегированные метрики о запросах / зависимостей до выборки происходит. | Да

Ознакомьтесь с [настраиваемыми настройками для `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) самого актуального списка.

### <a name="sampling"></a>Выборка

Служба анализа приложений Для сотрудников поддерживает как фиксированную, так и адаптивную выборку. Адаптивная выборка включена по умолчанию. Настройка выборки для службы обслуживания работников выполняется так же, как и для [ASP.NET основных приложений.](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)

### <a name="adding-telemetryinitializers"></a>Добавление телеметрии Первоначальные

Используйте [инициализаторы телеметрии,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) когда требуется определить свойства, которые отправляются со всей телеметрией.

Добавить `TelemetryInitializer` любой `DependencyInjection` новый в контейнер и SDK автоматически добавить их в `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Удаление телеметрии Первоначальные

Инициали телеметрии присутствуют по умолчанию. Чтобы удалить все или конкретные инициали телеметрии, используйте следующий код образца *после* вызова. `AddApplicationInsightsTelemetryWorkerService()`

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Добавление телеметрических процессоров

Вы можете добавить пользовательские `TelemetryConfiguration` телеметрические `AddApplicationInsightsTelemetryProcessor` процессоры с помощью метода расширения на `IServiceCollection`. Вы используете телеметрические процессоры в [продвинутых сценариях фильтрации,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) чтобы обеспечить более прямой контроль над тем, что включено или исключено из телеметрии, которые вы отправляете в службу Application Insights. Используйте следующий пример.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Настройка или удаление телеметрических модулей по умолчанию

Application Insights использует модули телеметрии для автоматического сбора телеметрии о конкретных рабочих нагрузках, не требуя ручного отслеживания.

Следующие модули автоматического сбора включены по умолчанию. Эти модули отвечают за автоматический сбор телеметрии. Вы можете отключить или настроить их, чтобы изменить их поведение по умолчанию.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Для настройки `TelemetryModule`любого по `ConfigureTelemetryModule<T>` умолчанию используйте метод расширения, `IServiceCollection`как показано в следующем примере.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Настройка телеметрического канала

Канал по `ServerTelemetryChannel`умолчанию . Вы можете переопределить его, как показано в следующем примере.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Отключить телеметрию динамически

Если вы хотите отключить телеметрию условно и динамично, вы можете разрешить `TelemetryConfiguration` экземпляр с ASP.NET `DisableTelemetry` контейнера для впрыска зависимости Core в любом месте вашего кода и установить флаг на нем.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Как отследить телеметрию, которая не собирается автоматически?

Получить экземпляр `TelemetryClient` с помощью впрыска `TrackXXX()` конструктора, и вызвать необходимый метод на нем. Мы не рекомендуем создавать `TelemetryClient` новые экземпляры. Однотонный `TelemetryClient` экземпляр уже зарегистрирован `DependencyInjection` в контейнере, который разделяет `TelemetryConfiguration` с остальной частью телеметрии. Создание нового `TelemetryClient` экземпляра рекомендуется только в том случае, если ему нужна конфигурация, отделенная от остальной телеметрии.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Могу ли я использовать Visual Studio IDE для бортового проекта Application Insights to a Worker Service?

Внештатная студия IDE в настоящее время поддерживается только для ASP.NET/ASP.NET основных приложений. Этот документ будет обновляться при поддержке Visual Studio для приложений обслуживания на борту.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Могу ли я включить мониторинг applicationInsight с помощью таких инструментов, как Status Monitor?

Нет. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) в настоящее время поддерживают только ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Если я запускаю приложение в Linux, поддерживаются ли все функции?

Да. Поддержка функций для этого SDK одинакова на всех платформах, за следующими исключениями:

* Счетчики производительности поддерживаются только в Windows, за исключением процессора Process/Memory, показанного в Live Metrics.
* Несмотря `ServerTelemetryChannel` на то, что приложение включено по умолчанию, если приложение работает в Linux или MacOS, канал автоматически не создает локальную папку хранения, чтобы временно сохранить телеметрию, если есть проблемы с сетью. Из-за этого ограничения телеметрия теряется при наличии временных проблем с сетью или сервером. Чтобы обойти эту проблему, назначайте локальную папку для канала:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Примеры приложений

[Основное консольное приложение .NET](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Используйте этот пример, если вы используете консольное приложение, написанное в .NET Core (2.0 или выше) или .NET Framework (4.7.2 или выше)

[Основные фоновые задачи ASP .NET с hostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Используйте этот образец, если вы находитесь в Asp.Net Core 2.1/2.2, и создание фоновых задач в качестве официального руководства [здесь](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[.NET Core 3.0 Служба работников](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Используйте этот пример, если у вас есть приложение .NET Core 3.0 Служба обслуживания работников в рамках официального руководства [здесь](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

[Читайте и вносите свой вклад в код](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Следующие шаги

* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для детального представления производительности и использования приложения.
* [Отслеживание дополнительных зависимостей не отслеживается автоматически.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Обогащать или фильтр авто собранной телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
* [Инъекции зависимости в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
