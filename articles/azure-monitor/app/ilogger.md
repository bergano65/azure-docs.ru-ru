---
title: Исследуйте журналы трасс .NET с помощью ILogger - Azure Application Insights
description: Образцы использования поставщика Azure Application Insights ILogger с ASP.NET приложениями Core и Console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276382"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider для журналов .NET Core ILogger

ASP.NET Core поддерживает API для регистрации, который работает с различными типами встроенных и сторонних поставщиков журнальных данных. Регистрация осуществляется путем вызова **журнала ()** или варианта его на *экземплярах ILogger.* В этой статье показано, как использовать *ApplicationInsightsLoggerProvider* для захвата журналов ILogger в консолях и ASP.NET основных приложений. В этой статье также описывается, как ApplicationInsightsLoggerProvider интегрируется с другими приложениями Исследования телеметрии.
Дополнительные сведения см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET основные приложения

ApplicationInsightsLoggerProvider включен по умолчанию в [версии Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1 (и позже), когда вы включите регулярный мониторинг Application Insights с помощью любого из методов:

- Позвонив в метод расширения **UseApplicationInsights** на IWebHostBuilder (теперь устаревший)
- Позвонив в метод расширения **AddApplicationInsightsTelemetry** на IServiceCollection

ILogger журналы, которые ApplicationInsightsLoggerProvider захватывает подлежат той же конфигурации, как и любой другой телеметрии, которые собираются. Они имеют тот же набор Telemetry Initializers и TelemetryProcessors, используют тот же TelemetryChannel, и коррелируются и пробы таким же образом, как и другие телеметрии. Если вы используете версию 2.7.1 или позже, для захвата журналов ILogger не требуется никаких действий.

Только *предупреждения* или выше журналы ILogger (из всех [категорий)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)отправляются в Application Insights по умолчанию. Но вы можете [применить фильтры, чтобы изменить это поведение.](#control-logging-level) Для захвата журналов ILogger с **Program.cs** или **Startup.cs**требуются дополнительные шаги. (См. [Захват журналов ILogger из Startup.cs и Program.cs в приложениях ASP.NET Core.)](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)

Если вы используете более раннюю версию Microsoft.ApplicationInsights.AspNet SDK или хотите просто использовать ApplicationInsightsLoggerProvider без какого-либо другого мониторинга Исследования приложений, используйте следующую процедуру:

1. Установите пакет NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Измените **Program.cs,** как показано здесь:

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
                   // Providing an instrumentation key here is required if you're using
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

Код в шаге `ApplicationInsightsLoggerProvider`2 настраивает . Следующий код показывает пример класса `ILogger` контроллера, который использует для отправки журналов. Эти журналы захвачены Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Захват журналов ILogger из Startup.cs и Program.cs в приложениях core ASP.NET

> [!NOTE]
> В ASP.NET Core 3.0 и позже, `ILogger` это больше не возможно вводить в Startup.cs и Program.cs. Дополнительные сведения см. в статье https://github.com/aspnet/Announcements/issues/353.

Новый ApplicationInsightsLoggerProvider может фиксировать журналы с самого начала конвейера приложения-стартапа. Хотя ApplicationInsightsLoggerProvider автоматически включен в Application Insights (начиная с версии 2.7.1), он не имеет ключа приборов, настроенного до более позднего конвейера. Таким образом, будут захвачены только журналы из **диспетчера**/других классов. Чтобы захватить каждый журнал, начиная с **Program.cs** и **Startup.cs** себя, необходимо явно включить ключ приборов для ApplicationInsightsLoggerProvider. Кроме того, *TelemetryConfiguration* не полностью настроена при входе в **систему** Program.cs или **Startup.cs** себя. Таким образом, эти журналы будут иметь минимальную конфигурацию, которая использует InMemoryChannel, без выборки, и никаких стандартных инициалии телеметрии или процессоров.

Следующие примеры демонстрируют эту возможность с **Program.cs** и **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Пример Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Мигрировать из старого ApplicationInsightsLoggerProvider

Версии Microsoft.ApplicationInsights.AspNet SDK до 2.7.1 поддерживали поставщика журналов, который устарел. Этот провайдер был включен с помощью метода расширения **AddApplicationInsights ()** ILoggerFactory. Мы рекомендуем вам перейти к новому поставщику, который включает в себя два шага:

1. Удалите вызов *ILoggerFactory.AddApplicationInsights ()* из метода **Startup.Configure()** во избежание двойной регистрации.
2. Повторно применять любые правила фильтрации в коде, потому что они не будут соблюдаться новым поставщиком. Перегрузки *ILoggerFactory.AddApplicationInsights ()* приняли минимальные функции LogLevel или фильтра. С новым поставщиком фильтрация является частью самой платформы журналов. Это не делается поставщиком Application Insights. Таким образом, любые фильтры, которые предоставляются через *ILoggerFactory.AddApplicationInsights ()* перегрузки должны быть удалены. И правила фильтрации должны быть предоставлены, следуя инструкциям [уровня регистрации control.](#control-logging-level) Если вы *используете appsettings.json* для фильтрации журналов, он будет продолжать работать с новым поставщиком, потому что оба используют тот же псевдоним поставщика, *ApplicationInsights*.

Вы все еще можете использовать старый поставщик. (Он будет удален только в основной версии изменения до 3. *xx*.) Но мы рекомендуем вам перейти к новому поставщику по следующим причинам:

- Предыдущему поставщику не хватает поддержки [областей журналов.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes) В новом поставщике свойства из области автоматически добавляются в качестве пользовательских свойств к собранной телеметрии.
- Теперь журналы можно зафиксировать гораздо раньше в конвейере запуска приложений. Теперь можно фиксировать журналы из классов **программы** и **запуска.**
- С новым поставщиком фильтрация выполняется на уровне самой инфраструктуры. Вы можете фильтровать журналы поставщику Application Insights так же, как и другим поставщикам, включая встроенных поставщиков, таких как Console, Debug и так далее. Вы также можете применить одни и те же фильтры к нескольким поставщикам.
- В ASP.NET Core (2.0 и более поздний срок) рекомендуемый способ [включения поставщиков журнальных данных](https://github.com/aspnet/Announcements/issues/255) — это использование методов расширения на ILoggingBuilder **в** Program.cs себя.

> [!Note]
> Новый поставщик доступен для приложений, ориентированных на NETSTANDARD2.0 или позже. Если приложение нацелено на старые версии .NET Core, такие как .NET Core 1.1, или если оно нацелено на рамочную систему .NET, продолжайте использовать старый провайдер.

## <a name="console-application"></a>Консольное приложение

> [!NOTE]
> Существует новое приложение Исследования SDK называется [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) который может использоваться для включения приложения Исследования (ILogger и другие приложения Исследования телеметрии) для любых консольных приложений. Рекомендуется использовать этот пакет и связанные с ним инструкции [отсюда](../../azure-monitor/app/worker-service.md).

Следующий код показывает пример консоли приложения, настроенного для отправки следов ILogger в Application Insights.

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
        // Create the DI container.
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

В этом примере `Microsoft.Extensions.Logging.ApplicationInsights`используется автономный пакет. По умолчанию эта конфигурация использует "голый минимум" TelemetryConfiguration для отправки данных в Application Insights. Минимум означает, что InMemoryChannel — это используемый канал. Там нет выборки и нет стандартных Телеметрия Initializers. Это поведение может быть отменено для консольного приложения, как показано в следующем примере.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

В следующем разделе показано, как переопределить Телеметрическую Конфигурацию по умолчанию с помощью **служб. Налаживание\<метода ТелеметрииКонфигурация>()** Этот пример `ServerTelemetryChannel` устанавливает и пробоотбор. Он добавляет пользовательский ITelemetryInitializer к TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Контроль уровня лесозаготовок

Infra ASP.NET Core *ILogger* имеет встроенный механизм для [нанесения фильтрации журналов.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) Это позволяет управлять журналами, которые отправляются каждому зарегистрированного поставщику, включая поставщика Application Insights. Фильтрация может быть выполнена либо в конфигурации (обычно с помощью файла *appsettings.json),* либо в коде. Этот объект обеспечивается самой структурой. Это не специфична для поставщика Application Insights.

Следующие примеры применяют правила фильтра к ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Создание правил фильтра в конфигурации с appsettings.json

Для ApplicationInsightsLoggerProvider, псевдоним поставщика `ApplicationInsights`. Следующий раздел *appsettings.json* поручает поставщикам журналов, как правило, регистрироваться на уровне *Предупреждение* и выше. Затем он переопределяет категории `ApplicationInsightsLoggerProvider` журналов, которые начинаются с "Microsoft" на уровне *Ошибка* и выше.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Создание правил фильтра в коде

Следующий фрагмент кода настраивает журналы для *Предупреждения* и выше из всех категорий и для *ошибки* и выше из категорий, которые начинаются с "Microsoft", которые `ApplicationInsightsLoggerProvider`будут отправлены в . Эта конфигурация такая же, как и в предыдущем разделе в *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Каковы старые и новые версии ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) включал в себя встроенный ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), который был включен с помощью методов расширения **ILoggerFactory.** Этот поставщик помечен устаревшим из версии 2.7.1. Он будет полностью удален в следующем крупном изменении версии. Пакет [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) сам по себе не устарел. Это необходимо для мониторинга запросов, зависимостей и так далее.

Предлагаемой альтернативой является новый автономный пакет [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), который содержит улучшенный ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsights.ApplicationInsight) и методы расширения на ILoggerBuilder для его включения.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версия 2.7.1 принимает зависимость от нового пакета и позволяет ILogger захвата автоматически.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Почему некоторые журналы ILogger отображаются дважды в Application Insights?

Дублирование может произойти, если у вас есть старая (теперь устаревшая) версия ApplicationInsightsLoggerProvider `AddApplicationInsights` `ILoggerFactory`включена, вызывая. Проверьте, есть ли метод **настройки** следующее, и удалите его:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Если при отладке отладки `EnableDebugLogger` от *false* Visual Studio вы испытываете двойную запись, то в коде, который позволяет application Insights, вы можете использовать ся. Это дублирование и исправление имеет значение только при отладке приложения.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Я обновил до [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.7.1, и журналы из ILogger фиксируются автоматически. Как полностью отключить эту функцию?

Просмотрите раздел [уровня регистрации управления,](../../azure-monitor/app/ilogger.md#control-logging-level) чтобы узнать, как фильтровать журналы в целом. Для выключения ПриложенияInsightsLoggerProvider `LogLevel.None`используйте:

**В коде:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**В конфигурации:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Почему некоторые журналы ILogger не имеют тех же свойств, что и другие?

Application Insights захватывает и отправляет журналы ILogger с помощью той же ТелеметрииConfiguration, которая используется для любой другой телеметрии. Но есть исключение. По умолчанию TelemetryConfiguration не полностью настроена при входе в **систему** Program.cs или **Startup.cs.** Логи из этих мест не будут иметь конфигурацию по умолчанию, поэтому они не будут работать все TelemetryInitializers и TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Я использую автономный пакет Microsoft.Extensions.Logинг.ApplicationInsights, и я хочу войти некоторые дополнительные пользовательские телеметрии вручную. Как мне это сделать?

При использовании автономного `TelemetryClient` пакета не вводится в контейнер DI, поэтому необходимо `TelemetryClient` создать новую конфигурацию и использовать ту же конфигурацию, что и поставщик регистраторов, как показано в следующем коде. Это гарантирует, что та же конфигурация используется для всех пользовательских телеметрии, а также телеметрии от ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Если вы используете пакет Microsoft.ApplicationInsights.AspNetCore для включения application `TelemetryClient` Insights, измените этот код, чтобы попасть непосредственно в конструктор. Например, [см.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Какой тип телеметрии Application Insights производится из журналов ILogger? Или где я могу увидеть журналы ILogger в application Insights?

ApplicationInsightsLoggerProvider фиксирует журналы ILogger и создает из них TraceTelemetry. Если объект исключения передается методу **журнала ()** на ILogger, вместо TraceTelemetry создается *ExceptionTelemetry.* Эти элементы телеметрии можно найти в тех же местах, что и любые другие TraceTelemetry или ExceptionTelemetry для исследования приложений, включая портал, аналитику или локальный отладчик Visual Studio.

Если вы предпочитаете всегда отправлять TraceTelemetry, используйте этот фрагмент:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>У меня нет sDK, и я использую расширение Web Apps Azure для включения приложений Для моих ASP.NET основных приложений. Как использовать нового поставщика? 

Расширение Application Insights в веб-приложениях Azure использует нового поставщика. Можно изменить правила фильтрации в файле *appsettings.json* для вашего приложения.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Я использую автономный пакет Microsoft.Extensions.ApplicationInsights и включив поставщика Application Insights, позвонив **в конструктора. AddApplicationInsights ("ikey")**. Есть ли возможность получить ключ приборов от конфигурации?


Изменить Program.cs и appsettings.json следующим образом:

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

   Соответствующий `appsettings.json`раздел от:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Этот код требуется только при использовании автономного поставщика журналов. Для регулярного мониторинга Application Insights ключ приборов загружается автоматически с траектории конфигурации *ApplicationInsights: Instrumentationkey.* Appsettings.json должен выглядеть следующим образом:

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

* [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [журналы трассировки .NET в исследованиях приложений](../../azure-monitor/app/asp-net-trace-logs.md)
