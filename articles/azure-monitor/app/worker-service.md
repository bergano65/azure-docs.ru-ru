---
title: Application Insights для приложений службы рабочей роли (приложений, отличных от HTTP) | Документация Майкрософт
description: Мониторинг приложений .NET Core/. NET Framework, отличных от HTTP, с Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2019
ms.openlocfilehash: 386c171e4785fac2c7fa6da39f249e211f4c660c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893304"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights для приложений службы рабочей роли (приложений, отличных от HTTP)

Application Insights выпускает новый пакет SDK, именуемый `Microsoft.ApplicationInsights.WorkerService`, который лучше всего подходит для рабочих нагрузок, отличных от HTTP, таких как обмен сообщениями, фоновые задачи, консольные приложения и т. д. Эти типы приложений не имеют представления о входящем HTTP-запросе, таком как традиционное приложение ASP.NET/ASP.NET Core, и поэтому использование пакетов Application Insights для [ASP.NET](asp-net.md) или [ASP.NET Core](asp-net-core.md) приложений не поддерживается.

Новый пакет SDK не выполняет сбор данных телеметрии самим собой. Вместо этого он переносит другие хорошо известные Application Insights автособирающие, такие как [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [перфкаунтерколлектор](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [аппликатионинсигхтслоггингпровидер](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) и т. д. Этот пакет SDK предоставляет методы расширения на `IServiceCollection` для включения и настройки сбора данных телеметрии.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Пакет [SDK Application Insights для службы рабочей роли](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) лучше всего подходит для приложений, отличных от HTTP, независимо от того, где или как они выполняются. Если приложение работает и имеет сетевое подключение к Azure, можно собирать данные телеметрии. Мониторинг Application Insights поддерживается везде, где поддерживается .NET Core. Этот пакет можно использовать в новой добавленной [службе рабочей роли .NET Core 3,0](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [фоновых задачах в ASP.NET Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), консольных приложениях (.NET Core/.NET Framework) и т. д.

## <a name="prerequisites"></a>Технические условия

Допустимый ключ инструментирования Application Insights. Этот ключ необходим для отправки любых данных телеметрии в Application Insights. Если необходимо создать новый Application Insights ресурс для получения ключа инструментирования, см. раздел [Создание ресурса Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Использование пакета SDK для Application Insights для служб Worker

1. Установите пакет [Microsoft. ApplicationInsights. воркерсервице](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.
   В следующем фрагменте кода показаны изменения, которые необходимо добавить в файл `.csproj` проекта.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.2" />
    </ItemGroup>
```

1. Вызовите метод расширения `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` для `IServiceCollection`, указав ключ инструментирования. Этот метод должен вызываться в начале приложения. Точное расположение зависит от типа приложения.

1. Извлеките экземпляр `ILogger` или `TelemetryClient` из контейнера внедрения зависимостей (DI), вызвав `serviceProvider.GetRequiredService<TelemetryClient>();` или используя внедрение конструктора. Этот шаг вызывает настройку модулей `TelemetryConfiguration` и автосбора.

Конкретные инструкции для каждого типа приложений описаны в следующих разделах.

## <a name="net-core-30-worker-service-application"></a>Приложение рабочей службы для .NET Core 3,0

Полный [Пример общего доступа](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Загрузка и установка [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Создание нового проекта рабочей службы с помощью нового шаблона проекта Visual Studio или командной строки `dotnet new worker`
3. Установите пакет [Microsoft. ApplicationInsights. воркерсервице](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.

4. Добавьте `services.AddApplicationInsightsTelemetryWorkerService();` в метод `CreateHostBuilder()` в классе `Program.cs`, как показано в следующем примере:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Измените `Worker.cs` в соответствии с приведенным ниже примером.

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

6. Настройте ключ инструментирования.

    Хотя ключ инструментирования можно указать в качестве аргумента для `AddApplicationInsightsTelemetryWorkerService`, рекомендуется указать ключ инструментирования в конфигурации. В следующем примере кода показано, как указать ключ инструментирования в `appsettings.json`. Убедитесь, что `appsettings.json` копируется в корневую папку приложения во время публикации.

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

Кроме того, можно указать ключ инструментирования в любой из следующих переменных среды.
`APPINSIGHTS_INSTRUMENTATIONKEY` или `ApplicationInsights:InstrumentationKey`

Например: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
ИЛИ `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Как правило, `APPINSIGHTS_INSTRUMENTATIONKEY` указывает ключ инструментирования для приложений, развернутых в веб-приложениях в виде веб-заданий.

> [!NOTE]
> Ключ инструментирования, указанный в коде, передается по переменной среды `APPINSIGHTS_INSTRUMENTATIONKEY`, которая WINS поверх других параметров.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core фоновых задач с размещенными службами

В [этом](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) документе описывается создание фоновых задач в приложении ASP.NET Core 2.1/2.2.

Полный [Пример общего доступа](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Установите пакет Microsoft. ApplicationInsights. Воркерсервице (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.
2. Добавьте `services.AddApplicationInsightsTelemetryWorkerService();` в метод `ConfigureServices()`, как показано в следующем примере:

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

Ниже приведен код для `TimedHostedService`, где находится логика фоновой задачи.

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
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Настройте ключ инструментирования.
   Используйте те же `appsettings.json` из примера, приведенного выше в примере рабочей службы .NET Core 3,0.

## <a name="net-corenet-framework-console-application"></a>Консольное приложение .NET Core/. NET Framework

Как упоминалось в начале этой статьи, новый пакет можно использовать для включения телеметрия Application Insights даже из обычного консольного приложения. Этот пакет предназначен для [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard), поэтому его можно использовать для консольных приложений в .net Core 2,0 или более поздней версии и .NET Framework 4.7.2 или более поздней версии.

Полный [Пример общего доступа](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Установите пакет Microsoft. ApplicationInsights. Воркерсервице (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) в приложение.

2. Измените Program.cs, как показано ниже.

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
                // Hence instrumentation key must be specified here.
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

Это консольное приложение также использует тот же `TelemetryConfiguration`по умолчанию, и его можно настроить так же, как примеры в предыдущем разделе.

## <a name="run-your-application"></a>Запуск приложения

Запустите приложение. В примере работники, приведенные выше, выполняют вызов HTTP каждую секунду в bing.com, а также создает несколько журналов с помощью ILogger. Эти строки упаковываются в `StartOperation` вызов `TelemetryClient`, который используется для создания операции (в этом примере `RequestTelemetry` с именем "Operation"). Application Insights соберет эти журналы ILogger (предупреждение или выше по умолчанию) и зависимости, и они будут связаны с `RequestTelemetry` с отношением "родители-потомки". Корреляция также работает с границей перекрестных процессов и сетей. Например, если вызов был выполнен для другого отслеживаемого компонента, он также будет связан с этим родителем.

Эту пользовательскую операцию `RequestTelemetry` можно рассматривать как эквивалент входящего веб-запроса в обычном веб-приложении. Хотя использование операции не является обязательным, оно подходит для [Application Insightsной модели данных корреляции](https://docs.microsoft.com/azure/azure-monitor/app/correlation) с `RequestTelemetry`, действующей как родительская операция, и все данные телеметрии, созданные в рабочей итерации, логически принадлежащие к одной и той же операции. Такой подход также гарантирует, что все создаваемые данные телеметрии (автоматически и вручную) будут иметь одинаковые `operation_id`. Поскольку выборка основана на `operation_id`, алгоритм выборки либо сохраняет, либо удаляет все данные телеметрии из одной итерации.

Ниже перечислены полные данные телеметрии, автоматически собираемые Application Insights.

### <a name="live-metrics"></a>Динамические метрики

Для быстрой проверки правильности настройки Application Insights мониторинга можно использовать [динамические метрики](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) . Это может занять несколько минут, прежде чем данные телеметрии будут отображаться на портале и в аналитике, в динамических метриках будет показано использование ЦП запущенного процесса практически в реальном времени. Он также может отображать другие данные телеметрии, такие как запросы, зависимости, трассировки и т. д.

### <a name="ilogger-logs"></a>Журналы ILogger

Журналы, созданные с помощью `ILogger` серьезности `Warning` или более, автоматически фиксируются. Следуйте инструкциям [ILogger](ilogger.md#control-logging-level) , чтобы настроить, какие уровни журнала фиксируются Application Insights.

### <a name="dependencies"></a>Зависимости

Коллекция зависимостей включена по умолчанию. В [этой](asp-net-dependencies.md#automatically-tracked-dependencies) статье объясняются автоматически собираемые зависимости, а также инструкции по отслеживанию вручную.

### <a name="eventcounter"></a>евенткаунтер

`EventCounterCollectionModule` включен по умолчанию и будет собираются набор счетчиков по умолчанию из приложений .NET Core 3,0. В руководстве по [евенткаунтер](eventcounters.md) представлен набор собираемых счетчиков. Он также содержит инструкции по настройке списка.

### <a name="manually-tracking-additional-telemetry"></a>Отслеживание дополнительных данных телеметрии вручную

Хотя пакет SDK автоматически собирает данные телеметрии, как описано выше, в большинстве случаев пользователь должен будет отправить дополнительные данные телеметрии в службу Application Insights. Рекомендуемый способ отслеживания дополнительных данных телеметрии заключается в получении экземпляра `TelemetryClient` от внедрения зависимостей и последующем вызове одного из поддерживаемых методов [API](api-custom-events-metrics.md) `TrackXXX()`. Другим типичным вариантом использования является [пользовательское отслеживание операций](custom-operations-tracking.md). Этот подход показан в примерах рабочих ролей выше.

## <a name="configure-the-application-insights-sdk"></a>Настройка пакета SDK для Application Insights

`TelemetryConfiguration` по умолчанию, используемый пакетом SDK для службы рабочей роли, аналогична автоматической конфигурации, используемой в приложении ASP.NET или ASP.NET Core, за вычетом Telemetryinitializer, используемой для обогащения данных телеметрии из `HttpContext`.

Можно настроить Application Insights SDK для службы рабочей роли, чтобы изменить конфигурацию по умолчанию. Пользователи пакета SDK Application Insights ASP.NET Core могут быть знакомы с изменением конфигурации с помощью ASP.NET Core встроенного [внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Пакет SDK для Воркерсервице также основан на аналогичных принципах. Сделайте почти все изменения конфигурации в разделе `ConfigureServices()`, вызвав соответствующие методы для `IServiceCollection`, как описано ниже.

> [!NOTE]
> При использовании этого пакета SDK изменение конфигурации путем изменения `TelemetryConfiguration.Active` не поддерживается, и изменения не будут отражены.

### <a name="using-applicationinsightsserviceoptions"></a>Использование Аппликатионинсигхтссервицеоптионс

Можно изменить несколько общих параметров, передав `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`, как показано в следующем примере:

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

Обратите внимание, что `ApplicationInsightsServiceOptions` в этом пакете SDK находится в пространстве имен `Microsoft.ApplicationInsights.WorkerService` в отличие от `Microsoft.ApplicationInsights.AspNetCore.Extensions` в пакете SDK для ASP.NET Core.

Часто используемые параметры в `ApplicationInsightsServiceOptions`

|Параметр | Описание | значение по умолчанию
|---------------|-------|-------
|енаблекуиккпулсеметрикстреам | Включить или отключить функцию Ливеметрикс | true
|енаблеадаптивесамплинг | Включение или отключение адаптивной выборки | true
|енаблехеартбеат | Функция "включить/отключить пульс", которая периодически (по умолчанию составляет 15 минут) отправляет пользовательскую метрику "Хеартбеатстате" со сведениями о среде выполнения, такими как версия .NET, сведения о среде Azure, если применимо, и т. д. | true
|аддаутоколлектедметрицекстрактор | Включите или отключите средство извлечения Аутоколлектедметрикс, которое представляет собой Телеметрипроцессор, который отправляет предварительно агрегированные метрики о запросах и зависимостях перед выполнением выборки. | true

Просмотрите [Настраиваемые параметры в `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) для наиболее актуального списка.

### <a name="sampling"></a>Выборка

Пакет SDK Application Insights для службы рабочей роли поддерживает как фиксированную, так и адаптивную выборку. Адаптивная выборка включена по умолчанию. Настройка выборки для службы рабочей роли выполняется так же, как и для [приложений ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Добавление Telemetryinitializer

Используйте [инициализаторы телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) , если нужно определить свойства, которые отправляются со всеми данными телеметрии.

Добавьте новые `TelemetryInitializer` в контейнер `DependencyInjection`, и пакет SDK автоматически добавит их в `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Удаление Telemetryinitializer

Инициализаторы телеметрии представлены по умолчанию. Чтобы удалить все или конкретные инициализаторы телеметрии, используйте следующий пример кода *после* вызова `AddApplicationInsightsTelemetryWorkerService()`.

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

### <a name="adding-telemetry-processors"></a>Добавление обработчиков данных телеметрии

Вы можете добавить пользовательские обработчики данных телеметрии для `TelemetryConfiguration` с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` на `IServiceCollection`. Вы используете обработчики данных телеметрии в [сценариях расширенной фильтрации](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) , чтобы обеспечить более прямое управление включенными или исключенными из данных телеметрии, отправляемых в службу Application Insights. Используйте следующий пример.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Настройка или удаление Телеметримодулес по умолчанию

Application Insights использует модули телеметрии для автоматического сбора данных телеметрии о конкретных рабочих нагрузках без необходимости отслеживания вручную.

Следующие модули автоматической коллекции включены по умолчанию. Эти модули отвечают за автоматическое сбор данных телеметрии. Их можно отключить или настроить для изменения их поведения по умолчанию.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Чтобы настроить любое `TelemetryModule`по умолчанию, используйте метод расширения `ConfigureTelemetryModule<T>` в `IServiceCollection`, как показано в следующем примере.

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

### <a name="configuring-telemetry-channel"></a>Настройка канала телеметрии

Канал по умолчанию — `ServerTelemetryChannel`. Его можно переопределить, как показано в следующем примере.

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

### <a name="disable-telemetry-dynamically"></a>Динамическое отключение телеметрии

Если вы хотите отключить данные телеметрии условно и динамически, вы можете разрешить `TelemetryConfiguration` экземпляр с контейнером внедрения зависимостей ASP.NET Core в любом месте кода и установить для него флажок `DisableTelemetry`.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Как можно отвестить данные телеметрии, которые не собираются автоматически?

Получите экземпляр `TelemetryClient` с помощью внедрения конструктора и вызовите для него необходимый метод `TrackXXX()`. Не рекомендуется создавать новые экземпляры `TelemetryClient`. Одноэлементный экземпляр `TelemetryClient` уже зарегистрирован в контейнере `DependencyInjection`, который совместно использует `TelemetryConfiguration` с остальными данными телеметрии. Создание нового экземпляра `TelemetryClient` рекомендуется только в том случае, если требуется конфигурация, отделяющая остальные данные телеметрии.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Можно ли использовать Visual Studio IDE для подключения Application Insights к проекту рабочей службы?

В настоящее время подключение интегрированной среды разработки Visual Studio поддерживается только для приложений ASP.NET/ASP.NET Core. Этот документ будет обновлен, когда Visual Studio отправит поддержку для адаптации приложений рабочей службы.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Можно ли включить Application Insights мониторинг с помощью таких средств, как монитор состояния?

Нет. В настоящее время [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и [Монитор состояния v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) поддерживают только ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Если я запускаю приложение в Linux, поддерживаются ли все функции?

Да. Поддержка функций для этого пакета SDK одинакова на всех платформах, за исключением следующих:

* Счетчики производительности поддерживаются только в Windows, за исключением ЦП или памяти процесса, показанными в динамических метриках.
* Хотя `ServerTelemetryChannel` включен по умолчанию, если приложение выполняется в Linux или MacOS, канал не создает папку локального хранилища, чтобы временно сохранять данные телеметрии в случае проблем с сетью. Из-за этого ограничения данные телеметрии теряются при наличии временных проблем с сетью или сервером. Чтобы обойти эту ошибку, настройте локальную папку для канала:

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

[Консольное приложение .NET Core](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Используйте этот пример, если вы используете консольное приложение, написанное на .NET Core (2,0 или более поздней версии) или .NET Framework (4.7.2 или более поздней версии).

[Фоновые задачи ASP .NET Core с хостедсервицес](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Используйте этот пример, если вы используете Asp.Net Core 2.1/2.2 и создаете фоновые задачи в соответствии с [официальным руководством.](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Рабочая служба .NET Core 3,0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Используйте этот пример, если у вас есть приложение рабочей службы .NET Core 3,0 в соответствии с [официальным руководством](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Дальнейшие действия

* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для получения подробного представления о производительности и использовании приложения.
* [Следите за автоматической](../../azure-monitor/app/auto-collect-dependencies.md)отслеживанием дополнительных зависимостей.
* [Расширить или отфильтровать автоматические собираемые данные телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
* [Внедрение зависимостей в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
