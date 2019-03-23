---
title: Изучение журналов трассировки .NET в Azure Application Insights с помощью ILogger
description: Примеры использования реализации ILogger для Azure Application Insights с приложениями ASP.NET Core и консольными приложениями.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: deaddfbd27c4ffe6738988c6368ce4f9c3a7fa78
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359149"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core поддерживает API ведения журнала, который совместим со множеством встроенных и сторонних соответствующих решений. В этой статье описано, как управлять ведением журнала с помощью реализации ILogger для Application Insights в приложениях ASP.NET Core и консольных приложениях. См. дополнительные сведения о [ведении журнала с помощью ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Консольное приложение

Ниже приведен пример консольного приложения, настроенного на отправку трассировок ILogger в Application Insights.

Установленные пакеты:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Приложение ASP.NET Core

Ниже приведен пример приложения ASP.NET Core, настроенного на отправку трассировок ILogger в Application Insights. Этот пример можно использовать для отправки трассировок ILogger из Program.cs, Startup.cs или любой другой логики контроллера или приложения.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up by Application Insights.  
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

В обоих примерах выше используется отдельный пакет Microsoft.Extensions.Logging.ApplicationInsights. По умолчанию с этой конфигурацией используется минимальное возможное значение `TelemetryConfiguration` для отправки данных в Application Insights, то есть канал `InMemoryChannel` без выборки и стандартных значений TelemetryInitializer. Для консольного приложения это поведение можно изменить, как показано в примере ниже.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

В разделе ниже показано, как изменить параметр `TelemetryConfiguration`, установленный по умолчанию. В этом примере настраивается `ServerTelemetryChannel`, выборка и пользовательское значение `ITelemetryInitializer`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Хотя в приложении ASP.NET Core можно использовать описанный выше подход, более приемлемым вариантом будет комбинировать обычный мониторинг приложения (запросы, зависимости и т. д.) с записью с помощью ILogger, как показано ниже.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Добавьте следующую строку в метод `ConfigureServices`. Этот код включит обычный мониторинг приложения со стандартной конфигурацией (ServerTelemetryChannel, LiveMetrics, запрос и зависимости, корреляция и т. д.).

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

В этом примере `ApplicationInsightsLoggerProvider` использует ту же конфигурацию, что и средства обычного мониторинга приложения. Поэтому и для трассировок `ILogger`, и для других данных телеметрии (запросы, зависимости и т. д.) будет использоваться тот же самый набор значений `TelemetryInitializers`, `TelemetryProcessors`, и `TelemetryChannel`. Корреляция и выборка будут выполняться (или не проводиться) для них одинаково.

Но есть одно исключение. Стандартный параметр `TelemetryConfiguration` не настраивается полностью при регистрации в журнал непосредственно из `Program.cs` или `Startup.cs`, поэтому у этих журналов не будет стандартной конфигурации. Но все остальные журналы (например, журналы контроллеров, моделей и т. д.) будут иметь общую конфигурацию.

## <a name="control-logging-level"></a>Уровень ведения журнала управления

Помимо фильтрации журналов на коде, как в приведенных выше примерах, можно также для управления уровнем ведения журнала Application Insights записывает, изменив `appsettings.json`. [ASP.NET, ведение журнала документации основы](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) показано, как это сделать. Специально для Application Insights — имя псевдонима поставщика `ApplicationInsights`, как показано в следующем примере, который настраивает `ApplicationInsights` для записи только журналы `Warning` и более поздних версий из всех категорий.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>Дальнейшие действия

См. также:

- [Ведение журналов с помощью ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Журналы трассировки .NET](../../azure-monitor/app/asp-net-trace-logs.md)
