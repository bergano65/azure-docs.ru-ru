---
title: Изучение журналов трассировки .NET в Azure Application Insights с помощью ILogger
description: Примеры использования Azure Application Insights ILogger поставщика с помощью приложений ASP.NET Core и консольных приложений.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901078"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core ILogger logs

ASP.NET Core поддерживает API ведения журнала, который работает с различными видами ведение журналов встроенных, так и сторонних поставщиков. Ведение журнала осуществляется путем вызова Log() или его вариант для `ILogger` экземпляров. В этой статье показано, как использовать `ApplicationInsightsLoggerProvider` для захвата `ILogger` записывает в консоль и приложений ASP.NET Core. В этой статье также описывается как `ApplicationInsightsLoggerProvider` интегрируется с другими данными телеметрии Application Insights.
Чтобы узнать дополнительные ведения журналов в Asp.Net Core, см. в разделе [в этой статье](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Приложения ASP.NET Core

Начиная с [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версии и более поздних версий, `ApplicationInsightsLoggerProvider` включена по умолчанию при включении регулярных мониторинг Application Insights с помощью стандартных методов - с вызов `UseApplicationInsights` метод расширения в IWebHostBuilder или `AddApplicationInsightsTelemetry` IServiceCollection метода расширения. `ILogger` журналы, захваченные `ApplicationInsightsLoggerProvider` подвержены же конфигурацией, как любые другие данные телеметрии, собранных. Например, они имеют тот же набор `TelemetryInitializer`s, `TelemetryProcessor`s, использует тот же `TelemetryChannel`и будут коррелированных событий и выборка в так же, как каждый других данных телеметрии.  Если вы используете в этой версии пакета SDK или более поздней версии, то никаких действий не требуется для записи `ILogger` журналы.

По умолчанию только `ILogger` журналы `Warning` или выше (из всех категорий) отправляются в Application Insights. Это поведение можно изменить путем применения фильтров, как показано [здесь](#control-logging-level). Также дополнительные действия не требуются, если `ILogger` журналов из `Program.cs` или `Startup.cs` , которые должны отслеживаться как показано [здесь](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Если используется более ранняя версия пакета SDK Microsoft.ApplicationInsights.AspNet или нужно просто использовать ApplicationInsightsLoggerProvider, без любые другие мониторинг Application Insights, выполните следующие действия.

1. Установите пакет nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. Изменение `Program.cs` как показано ниже

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

Приведенный выше код настроит `ApplicationInsightsLoggerProvider`. Ниже приведен пример класса контроллера, который использует `ILogger` для отправки журналов, которые отслеживаются с ApplicationInsights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Записи журналов ILogger из Startup.cs, Program.cs в приложениях Asp.Net Core

С помощью нового ApplicationInsightsLoggerProvider можно собирать журналы из на раннем этапе в конвейер запуска приложения. Даже хотя является ApplicationInsightsLoggerProvider автоматически включили Application Insights (из 2.7.0-beta3 и более поздние версии), нет настройка ключа инструментирования, пока не будет далее в конвейере, так что лишь журналы из контроллера / будут записаны другие классы. Чтобы записать туда, начиная с `Program.cs` и `Startup.cs` , один нужно явно включить ApplicationInsightsLoggerProvider с использованием ключа инструментирования. Также важно отметить, что `TelemetryConfiguration` не полностью настроить, когда записывает в журнал чего-либо из `Program.cs` или `Startup.cs` сам, поэтому эти журналы будут использовать исходного минимальная конфигурация, которая использует InMemoryChannel, ни одного образца, а не стандартной телеметрии инициализаторы или процессоров.

Ниже приведены примеры `Program.cs` и `Startup.cs` благодаря этой возможности.

#### <a name="example-programcs"></a>Пример Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Пример Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Миграция из старых ApplicationInsightsLoggerProvider

Пакет SDK Microsoft.ApplicationInsights.AspNet версии перед 2.7.0-beta2, поддерживается поставщика ведения журнала, который теперь является устаревшим. Этот поставщик были включены в `AddApplicationInsights()` метод расширения из `ILoggerFactory`. Этот поставщик устарел, и пользователям предлагается перейти на новый поставщик. Миграция состоит из двух этапов.

1. Удалите вызов ILoggerFactory.AddApplicationInsights() из `Startup.Configure()` метод, чтобы избежать двойной ведения журнала.
2. Повторно примените правила фильтрации в коде, как они будут соблюдаться не поддерживается новым поставщиком. Перегрузки ILoggerFactory.AddApplicationInsights() заняло минимальное LogLevel или фильтр функции. С новым поставщиком, фильтрация является частью платформы ведения журнала и не выполняется поставщиком Application Insights. Поэтому любые фильтры, предоставляемого `ILoggerFactory.AddApplicationInsights()` перегрузки должны быть удалены, и правила фильтрации должны предоставляться следующие [эти](#control-logging-level) инструкции. Если вы используете `appsettings.json` для фильтрации ведения журнала, она будет продолжать работать с новым поставщиком, так как они используют тот же псевдоним поставщик - **ApplicationInsights**.

Хотя по-прежнему используется старый поставщик (он является устаревшим и будет удален только в основной номер версии изменение 3.xx), переход на более новый поставщик настоятельно рекомендуется из-за следующих причин.

1. Предыдущий поставщик не было поддержки из [областей](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). В новый поставщик свойства из области автоматически добавляется в качестве пользовательских свойств для собранных данных телеметрии.
2. Теперь можно записать журналы гораздо выше в конвейер запуска приложения. Например, Теперь можно записать журналы из классов программа и запуска.
3. С помощью нового поставщика которая выполняется на уровне платформы, сам. Фильтрация журналов поставщику Application Insights можно выполнить в точно так же, как и для других поставщиков, включая встроенные поставщики, такие как консоли отладки и так далее. Можно также применять же фильтры для нескольких поставщиков.
4. [Рекомендуется](https://github.com/aspnet/Announcements/issues/255) в ASP.NET Core (начиная с версии 2.0) способ включения поставщиков ведения журнала — с помощью методов расширения ILoggingBuilder в `Program.cs` сам.

> [!Note]
> Новый поставщик доступен для приложений, предназначенных для `NETSTANDARD2.0` или более поздней версии. Если приложение предназначено для более старых версиях .NET Core, таких как .NET Core 1.1 или для платформы .NET Framework, по-прежнему использовать старый поставщик.

## <a name="console-application"></a>Консольное приложение

В следующем коде показан пример консольного приложения, настроить для отправки `ILogger` трассировок в Application Insights.

Установленные пакеты:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

В приведенном выше примере, изолированный пакет `Microsoft.Extensions.Logging.ApplicationInsights` используется. По умолчанию с этой конфигурацией используется минимальное возможное значение `TelemetryConfiguration` для отправки данных в Application Insights, то есть канал `InMemoryChannel` без выборки и стандартных значений TelemetryInitializer. Для консольного приложения это поведение можно изменить, как показано в примере ниже.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

В следующем разделе показано, как переопределить значение по умолчанию `TelemetryConfiguration` с помощью `services.Configure<TelemetryConfiguration>()` метод. Этот пример устанавливает `ServerTelemetryChannel`, выборки и добавляет пользовательский `ITelemetryInitializer` для `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Уровень ведения журнала управления

Asp.Net Core `ILogger` инфраструктуры имеет встроенный механизм для применения [фильтрации](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) журналов, что позволяет пользователям контролировать журналов, отправляемых в каждой зарегистрированных поставщиков, включая поставщик Application Insights. Такая фильтрация можно сделать либо в конфигурации (обычно с помощью `appsettings.json` файл) или в коде. Эта функция позволяет обеспечивается самой платформы и не относится к поставщику Application Insights.

Примеры применения правил фильтрации ApplicationInsightsLoggerProvider приведены ниже.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Создание правила фильтрации в конфигурации с appsettings.json

Для ApplicationInsightsLoggerProvider, имеет псевдоним поставщика `ApplicationInsights`. Ниже показано статьи `appsettings.json` настраивает журналы `Warning` и более поздних версий из всех категорий `Error` и более поздних версий из категорий, начиная с «Microsoft», который должны отправляться `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Создание правила фильтрации в коде

Ниже кода фрагмент кода настраивает журналы `Warning` и более поздних версий из всех категорий `Error` и выше категорий, начиная с «Microsoft», который должны отправляться `ApplicationInsightsLoggerProvider`. Этот файл конфигурации — это так же, как в конфигурации `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

*1. Что такое ApplicationInsightsLoggerProvider старые и новые?*

* [Пакет SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) в состав встроенных ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), который был создан с помощью ILoggerFactory методы расширения. Этот поставщик помечен как устаревший с 2.7.0-beta2 и более поздние версии и будет полностью удалена в следующей смене основного номера версии. [Это](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) упаковать сам не устаревший и необходим для включения мониторинга запросов, зависимостей и т.д.

* Предлагаемые альтернативой является новый изолированный пакет [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), содержащий улучшенные ApplicationInsightsLoggerProvider () Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) и методы расширения на ILoggerBuilder для его включения.

* [Пакет SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версии и более поздних версий будет зависимость от выше пакета, а также позволяет `ILogger` записи автоматически.

*2. Я вижу некоторые `ILogger` журналы отображаются в Application Insights дважды?*

* Это дублирование возможно в том случае, если у вас есть более старой версии (устаревшая) `ApplicationInsightsLoggerProvider` включена, вызвав `AddApplicationInsights` на `ILoggerFactory`. Установите флажок, если ваш `Configure` имеет следующие метод и удалите его.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Если у вас возникли double ведения журнала при отладке из Visual Studio, затем изменить код, используемый для следующим образом, включите Application Insights, установив `EnableDebugLogger` равным false. Эта проблема дублирования и исправление применяется только при отладке приложения.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Я обновил для [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версии и я теперь вижу, журналы из `ILogger` регистрируются автоматически. Как можно отключить эту функцию полностью?*

* См. в разделе [это](../../azure-monitor/app/ilogger.md#control-logging-level) раздел, чтобы знать, как фильтровать журналы в целом. Чтобы отключить ApplicationInsightsLoggerProvider используйте `LogLevel.None` для него.

  В коде

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  В файле конфигурации

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Я вижу некоторые `ILogger` журналы не того же свойства, что другим пользователям?*

* Application Insights снимки и отправляет `ILogger` журналы, используя те же `TelemetryConfiguration` для каждого других данных телеметрии. Есть исключение этого правила. Значение по умолчанию `TelemetryConfiguration` не полностью настроить, когда записывает в журнал чего-либо из `Program.cs` или `Startup.cs` сам, поэтому журналы из этих мест не будет конфигурации по умолчанию и поэтому не будет работать все `TelemetryInitializer`s и `TelemetryProcessor`s.

*5. Я использую Microsoft.Extensions.Logging.ApplicationInsights изолированный пакет, и я хочу войти некоторые дополнительные пользовательские телеметрические данные вручную. Как следует сделать?*

* При использовании изолированного пакета `TelemetryClient` не внедряется в контейнер внедрения Зависимостей, поэтому пользователи должны создавать новый экземпляр класса `TelemetryClient` , используя ту же конфигурацию используемый поставщик средства ведения журнала, как показано ниже. Это гарантирует, что для всех пользовательских данных телеметрии, а также полученных ILogger используется такой же конфигурацией.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Обратите внимание, что, если пакет Microsoft.ApplicationInsights.AspNetCore используется для включения Application Insights, затем приведенный выше пример может изменяться для получения `TelemetryClient` непосредственно в конструкторе. См. в разделе [это](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) полный пример.


*6. Application Insights тип телеметрии, полученных из `ILogger` журналы? или где можно просмотреть `ILogger` журналов в Application Insights?*

* Захватывает ApplicationInsightsLoggerProvider `ILogger` входит и создает `TraceTelemetry` от него. Если объект исключения передается в метод Log() на ILogger, то вместо `TraceTelemetry`, `ExceptionTelemetry` создается. Эти элементы телеметрии можно найти там же, как любой другой `TraceTelemetry` или `ExceptionTelemetry` для Application Insights, включая портал аналитики и локальный отладчик Visual Studio.
Если вы хотите всегда отправлять `TraceTelemetry`, затем использовать фрагмент ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. У меня нет установлен пакет SDK, и я использую расширение веб-приложения Azure, чтобы включить Application Insights для моих приложений Asp.Net Core. Использование нового поставщика*

* Расширение Application Insights в веб-приложения Azure с помощью старого поставщика. Правила фильтрации можно изменить в `appsettings.json` для вашего приложения. Если вы хотите воспользоваться преимуществами нового поставщика, используйте инструментирования во время сборки, создавая зависимость nuget по SDK. В этом документе будет обновляться, когда расширение переключается на использование нового поставщика.

*8. Я с помощью изолированного пакета Microsoft.Extensions.Logging.ApplicationInsights и включение Application Insights поставщика вызывающий построителем. AddApplicationInsights("ikey"). Есть ли возможность получить ключ инструментирования из конфигурации?*


* Изменить `Program.cs` и `appsettings.json` как показано ниже.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

Соответствующий раздел из `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

Приведенный выше код является обязательным, только в том случае, при использовании автономного поставщика ведения журнала. Для регулярного мониторинга Application Insights, ключ инструментирования загружается автоматически из пути конфигурации `ApplicationInsights:Instrumentationkey` и `appsettings.json` должен выглядеть следующим образом.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

См. также:

* [Ведение журналов в Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Журналы трассировки .NET в Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
