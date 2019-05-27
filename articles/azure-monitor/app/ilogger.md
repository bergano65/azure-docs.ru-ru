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
ms.openlocfilehash: fd5a16334fff0319d7993fb2403a48d1777f6bce
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955340"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core ILogger logs

ASP.NET Core поддерживает API ведения журнала, который работает с различными видами ведение журналов встроенных, так и сторонних поставщиков. Ведение журнала осуществляется путем вызова **Log()** или его вариант на *ILogger* экземпляров. В этой статье демонстрируется использование *ApplicationInsightsLoggerProvider* собирать журналы ILogger, в консоли и приложений ASP.NET Core. В этой статье также описывается, как ApplicationInsightsLoggerProvider интегрируется с другими данными телеметрии Application Insights.
Дополнительные сведения см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Приложения ASP.NET Core

ApplicationInsightsLoggerProvider включена по умолчанию в [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версии (и более поздних версиях) при включении Регулярное отслеживание Application Insights через стандартный методы:
- Путем вызова **UseApplicationInsights** IWebHostBuilder метода расширения 
- Путем вызова **AddApplicationInsightsTelemetry** IServiceCollection метода расширения

Журналы ILogger, которые записывают ApplicationInsightsLoggerProvider подвержены той же конфигурации, как любые другие данные телеметрии, собираемые. Они имеют тот же набор Telemetryinitializer и Telemetryprocessor, использовать же TelemetryChannel и связаны друг с другом и выборка так же, как другие данные телеметрии. Если вы используете версию 2.7.0-beta3 или более поздней версии, никаких действий не требуется, чтобы собирать журналы ILogger.

Только *предупреждение* или более поздней версии журналы ILogger (из всех категорий) отправляются в Application Insights по умолчанию. Но вы можете [применить фильтры, чтобы изменить это поведение](#control-logging-level). Необходимы дополнительные действия, чтобы собирать журналы ILogger из **Program.cs** или **Startup.cs**. (См. в разделе [ILogger записи журналов из Startup.cs и Program.cs в приложениях ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Если вы используете более раннюю версию пакета SDK Microsoft.ApplicationInsights.AspNet или вы хотите просто использовать ApplicationInsightsLoggerProvider без любые другие мониторинг Application Insights, используйте следующую процедуру:

1. Установите пакет NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Изменить **Program.cs** следующим образом:

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

Код на шаге 2 настраивает `ApplicationInsightsLoggerProvider`. Ниже показан пример класса контроллера, который использует `ILogger` для отправки журналов. Журналы сохраняются службой Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Собирать журналы ILogger из Startup.cs и Program.cs в приложениях ASP.NET Core

Новый ApplicationInsightsLoggerProvider можно собирать журналы из на раннем этапе конвейера запуска приложения. Несмотря на то, что ApplicationInsightsLoggerProvider автоматически включается в Application Insights (начиная с версии 2.7.0-beta3), он не имеет ключ инструментирования, задать до более поздней версии в конвейере. Таким образом, только журналов из **контроллера**/ будут записаны другие классы. Для каждого журнала, начиная с записи **Program.cs** и **Startup.cs** , необходимо явно включить ключ инструментирования для ApplicationInsightsLoggerProvider. Кроме того *конфигурацией TelemetryConfiguration* не настроен полностью при входе в систему из **Program.cs** или **Startup.cs** сам. Поэтому эти журналы будет иметь минимальные конфигурации с InMemoryChannel ни одного образца и инициализаторы стандартной телеметрии и не процессоров.

В следующих примерах демонстрируется эту возможность с помощью **Program.cs** и **Startup.cs**.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Перенести из старого ApplicationInsightsLoggerProvider

Устаревшие версии пакета SDK Microsoft.ApplicationInsights.AspNet перед добавлением поставщика ведения журнала, который теперь 2.7.0-beta2. Этот поставщик был включен через **AddApplicationInsights()** ILoggerFactory метод расширения. Мы рекомендуем выполнить миграцию нового поставщика, который состоит из двух этапов:

1. Удалить *ILoggerFactory.AddApplicationInsights()* вызов из **Startup.Configure()** метод, чтобы избежать двойной ведения журнала.
2. Повторно примените правила фильтрации в коде, так как они будут соблюдаться не поддерживается новым поставщиком. Перегруженные версии *ILoggerFactory.AddApplicationInsights()* заняло минимальное LogLevel или фильтр функции. С новым поставщиком фильтрация является частью сама платформа ведения журнала. Не выполняется поставщиком Application Insights. Поэтому все фильтры, которые предоставляются через *ILoggerFactory.AddApplicationInsights()* перегрузки должны быть удалены. И правила фильтрации необходимо предоставить, следуя [управления уровнем ведения журнала](#control-logging-level) инструкции. Если вы используете *appsettings.json* для фильтрации ведения журнала, она будет продолжать работать с новым поставщиком, так как используют тот же псевдоним поставщика *ApplicationInsights*.

Можно по-прежнему использовать старый поставщик. (Он будет удален только в изменение основного номера версии 3. *xx*.) Но мы рекомендуем перейти на новый поставщик по следующим причинам:

- Предыдущий поставщик не предоставляет поддержку для [журнала областей](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). В новый поставщик свойства из области автоматически добавляется в качестве пользовательских свойств для собранных данных телеметрии.
- Теперь можно записать журналы гораздо выше в конвейер запуска приложения. Записывает в журналы из **программы** и **запуска** классов теперь можно записать.
- С новым поставщиком фильтрация выполняется на уровне платформы, сам. Можно фильтровать журналы, чтобы поставщик Application Insights так же как и для других поставщиков, включая встроенные поставщики, такие как консоли отладки и так далее. Также можно применить те же фильтры для нескольких поставщиков.
- В ASP.NET Core (версии 2.0 и более поздние версии), рекомендуемый способ [включить поставщики ведения журналов](https://github.com/aspnet/Announcements/issues/255) — с помощью методов расширения ILoggingBuilder в **Program.cs** сам.

> [!Note]
> Новый поставщик, доступных для приложений, предназначенных NETSTANDARD2.0 или более поздней версии. Если приложение предназначено для более старых версиях .NET Core, такие как .NET Core 1.1, или если оно предназначено для .NET Framework, по-прежнему использовать старый поставщик.

## <a name="console-application"></a>Консольное приложение

В следующем коде показано пример консольного приложения, настроенный для отправки ILogger трассировок в Application Insights.

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

В этом примере используется изолированный пакет `Microsoft.Extensions.Logging.ApplicationInsights`. По умолчанию в этой конфигурации используется «минимальный» конфигурацией TelemetryConfiguration для отправки данных в Application Insights. Минимальный набор означает, что InMemoryChannel канала, который используется. Отсутствует свойство TelemetryInitializers не выборкой, так и не стандартные. Это поведение можно переопределить для консольного приложения, как показано в следующем примере.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

В следующем разделе показано, как переопределить значение по умолчанию конфигурацией TelemetryConfiguration с помощью **служб. Настройка<TelemetryConfiguration>()** метод. Этот пример устанавливает `ServerTelemetryChannel` и выборки. Он добавляет пользовательский ITelemetryInitializer конфигурацией TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Уровень ведения журнала управления

ASP.NET Core *ILogger* инфраструктуры есть встроенный механизм для применения [фильтрации журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Это позволяет контролировать журналы, которые отправляются для каждого зарегистрированного поставщика, включая службу Application Insights. Фильтрацию можно сделать либо в конфигурации (обычно с помощью *appsettings.json* файл) или в коде. Эта функция позволяет обеспечивается сама платформа. Он не относится к поставщику Application Insights.

Следующие примеры правил фильтрации применяются к ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Создание правила фильтрации в конфигурации с appsettings.json

Для ApplicationInsightsLoggerProvider, имеет псевдоним поставщика `ApplicationInsights`. В следующем разделе *appsettings.json* настраивает журналы для *предупреждение* и более поздних версий из всех категорий и *ошибка* и выше категорий, которые начинаются с " Microsoft» отправку `ApplicationInsightsLoggerProvider`.

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

В следующем фрагменте кода настраивается журналы для *предупреждение* и более поздних версий из всех категорий, а также для *ошибка* и выше категорий, которые начинаются с «Microsoft», который должны отправляться `ApplicationInsightsLoggerProvider`. Эта конфигурация аналогична описанной в предыдущем разделе *appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Что такое старые и новые версии ApplicationInsightsLoggerProvider?

[Пакет SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) включены встроенные ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), которая была включена по  **ILoggerFactory** методы расширения. Этот поставщик помечен как устаревший с 2.7.0-beta2 версии. Она будет полностью удалена в следующей смене основного номера версии. [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) сам пакет не устаревший. Это необходимо для отслеживания запросов, зависимостей и т. д.

Предлагаемые альтернативой является новый изолированный пакет [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), который содержит улучшенные (ApplicationInsightsLoggerProvider Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) и методы расширения на ILoggerBuilder для его включения.

[Пакет SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версия зависящий от нового пакета и автоматически включает записи ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Почему некоторые журналы ILogger показаны два раза в Application Insights?

Дублирование может произойти, если у вас есть более раннюю версию (устаревшая) включена, вызвав ApplicationInsightsLoggerProvider `AddApplicationInsights` на `ILoggerFactory`. Установите флажок, если ваш **Настройка** имеет следующие метод и удалите его:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Если возникают double ведение журнала отладки из Visual Studio, установите `EnableDebugLogger` для *false* в коде, который позволяет Application Insights, как показано ниже. Это дублирование и исправление применяется только при отладке приложения.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Я обновил для [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 версии и журналы из ILogger регистрируются автоматически. Как отключить эту функцию полностью?

См. в разделе [управления уровнем ведения журнала](../../azure-monitor/app/ilogger.md#control-logging-level) раздел, чтобы узнать, как фильтровать журналы в целом. Чтобы отключить ApplicationInsightsLoggerProvider, используйте `LogLevel.None`:

**В коде:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**В файле конфигурации:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Почему некоторые журналы ILogger нет теми же свойствами, что другим пользователям?

Application Insights собирает и отправляет ILogger журналы с помощью одной конфигурацией TelemetryConfiguration, который используется для других данных телеметрии каждые. Но существует одно исключение. По умолчанию конфигурацией TelemetryConfiguration не настроен полностью при входе в систему из **Program.cs** или **Startup.cs**. Журналы из этих мест не в конфигурации по умолчанию, поэтому они не будут выполняться все Telemetryinitializer и Telemetryprocessor.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Я использую Microsoft.Extensions.Logging.ApplicationInsights изолированный пакет, и я хочу войти некоторые дополнительные пользовательские телеметрические данные вручную. Как следует сделать?

При использовании изолированного пакета `TelemetryClient` не внедряется в контейнер внедрения Зависимостей, поэтому вам нужно создать новый экземпляр класса `TelemetryClient` и использовать ту же конфигурацию в качестве использует поставщик средства ведения журнала, как показано в следующем коде. Это гарантирует, что все пользовательские данные телеметрии, а также данные телеметрии из ILogger используется такой же конфигурацией.

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
> При использовании пакета Microsoft.ApplicationInsights.AspNetCore как включить Application Insights, измените этот код, чтобы получить `TelemetryClient` непосредственно в конструкторе. Например, см. в разделе [часто задаваемых вопросов](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Какой тип данных телеметрии Application Insights создается из журналов ILogger? Или, где можно просмотреть журналы ILogger в Application Insights?

ApplicationInsightsLoggerProvider собирает информацию журналов ILogger и создает TraceTelemetry из них. Если передается объект исключения **Log()** метод ILogger, *ExceptionTelemetry* вместо TraceTelemetry создается. Эти элементы телеметрии можно найти же, как любой другой TraceTelemetry или ExceptionTelemetry для Application Insights, включая портал аналитики и локальный отладчик Visual Studio.

Если вы хотите всегда отправлять TraceTelemetry, используйте этот фрагмент кода: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>У меня нет установлен пакет SDK, и я использую расширение веб-приложений Azure для включения Application Insights для моих приложений ASP.NET Core. Использование нового поставщика 

Расширение Application Insights в веб-приложений Azure с помощью старого поставщика. Можно изменить правила фильтрации в *appsettings.json* файла для приложения. Чтобы воспользоваться преимуществами нового поставщика, используйте инструментирования во время сборки, создавая зависимость NuGet по SDK. В этой статье будет обновляться при расширении переключается на использование нового поставщика.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Я являюсь использованием изолированного пакета Microsoft.Extensions.Logging.ApplicationInsights и включение Application Insights поставщика путем вызова **построитель. AddApplicationInsights("ikey")**. Есть ли возможность получить ключ инструментирования из конфигурации?


Измените файл Program.cs и appsettings.json следующим образом:

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

   Соответствующий раздел из `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Этот код не требуется, только в том случае, при использовании автономного поставщика ведения журнала. Для регулярного мониторинга Application Insights, ключ инструментирования загружается автоматически из пути конфигурации *ApplicationInsights: Instrumentationkey*. AppSettings.JSON должен выглядеть следующим образом:

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
* [Журналы трассировки .NET в Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
