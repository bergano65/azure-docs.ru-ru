---
title: Изучение журналов трассировки .NET в Azure Application Insights с помощью ILogger
description: Примеры использования поставщика Azure Application Insights ILogger с ASP.NET Core и консольными приложениями.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 925264bb69093ab70465665e1d2da615a7a3e53d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261754"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>Аппликатионинсигхтслогжерпровидер для журналов ILogger для .NET Core

ASP.NET Core поддерживает API ведения журнала, который работает с различными типами встроенных и сторонних поставщиков ведения журналов. Ведение журнала выполняется путем вызова **log ()** или его варианта в экземплярах *ILogger* . В этой статье показано, как использовать *аппликатионинсигхтслогжерпровидер* для записи журналов ILogger в консольных и ASP.NET Core приложениях. В этой статье также описано, как Аппликатионинсигхтслогжерпровидер интегрируется с другими Application Insights телеметрии.
Дополнительные сведения см. в статье [Ведение журналов в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core приложения

Аппликатионинсигхтслогжерпровидер включен по умолчанию в [пакете SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.7.0-beta3 (и более поздних версий) при включении обычного мониторинга Application Insights с помощью любого из стандартных методов:
- Путем вызова метода расширения **UseApplicationInsights** для ивебхостбуилдер 
- Путем вызова метода расширения **аддаппликатионинсигхтстелеметри** для IServiceCollection

Журналы ILogger, для которых Аппликатионинсигхтслогжерпровидер записи, подчиняются той же конфигурации, что и другие собираемые данные телеметрии. Они имеют одинаковый набор Telemetryinitializer и TelemetryProcessors, используют один и тот же Телеметричаннел, а также сопоставляются и вычисляются с выборкой так же, как и с другими данными телеметрии. Если вы используете версию 2.7.0-beta3 или более позднюю, для записи журналов ILogger не требуется никаких действий.

Application Insights по умолчанию отправляются только *предупреждения* или более высокие журналы ILogger (из всех категорий). Но вы можете [Применить фильтры, чтобы изменить это поведение](#control-logging-level). Для записи журналов ILogger из **Program.CS** или **Startup.CS**требуются дополнительные действия. (См. раздел [захват журналов ILogger из Startup.cs и Program.cs в ASP.NET Core приложениях](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Если вы используете более раннюю версию пакета SDK для Microsoft. ApplicationInsights. AspNet или хотите использовать Аппликатионинсигхтслогжерпровидер без других Application Insights мониторинга, выполните следующую процедуру.

1. Установите пакет NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Измените **Program.CS** , как показано ниже:

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

Код на шаге 2 настраивает `ApplicationInsightsLoggerProvider`. В следующем коде показан пример класса контроллера, который использует `ILogger` для отправки журналов. Журналы фиксируются Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Запись журналов ILogger из Startup.cs и Program.cs в приложениях ASP.NET Core

> [!NOTE]
> В ASP.NET Core 3,0 и более поздних версий больше нельзя внедрять `ILogger` в Startup.cs и Program.cs. Дополнительные сведения см. в статье https://github.com/aspnet/Announcements/issues/353.

Новый Аппликатионинсигхтслогжерпровидер может записывать журналы с самого начала в конвейере запуска приложения. Хотя Аппликатионинсигхтслогжерпровидер автоматически включается в Application Insights (начиная с версии 2.7.0-beta3), ключ инструментирования не настраивается до последующего в конвейере. Таким образом, будут записываться только журналы из классов. другие **контроллера**. Чтобы записать каждый журнал, начиная с **Program.CS** и **Startup.CS** , необходимо явно включить ключ инструментирования для аппликатионинсигхтслогжерпровидер. Кроме того, *телеметриконфигуратион* не полностью настраивается при регистрации из **Program.CS** или **Startup.CS** . Поэтому эти журналы будут иметь минимальную конфигурацию, которая использует InMemoryChannel, без выборки и не имеет стандартных инициализаторов телеметрии или процессоров.

В следующих примерах демонстрируется эта возможность с помощью **Program.CS** и **Startup.CS**.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Миграция из старого Аппликатионинсигхтслогжерпровидер

Версии пакета SDK для Microsoft. ApplicationInsights. AspNet, предшествующие 2.7.0-2, поддерживали поставщик ведения журнала, который теперь устарел. Этот поставщик был включен с помощью метода расширения **аддаппликатионинсигхтс ()** объекта илогжерфактори. Рекомендуется выполнить миграцию на новый поставщик, который состоит из двух этапов:

1. Удалите вызов *илогжерфактори. аддаппликатионинсигхтс ()* из метода **Startup. configure ()** , чтобы избежать двойного протоколирования.
2. Повторно примените все правила фильтрации в коде, так как они не будут соответствовать новому поставщику. Перегрузки *илогжерфактори. аддаппликатионинсигхтс ()* заняли минимум LogLevel или функций фильтрации. При использовании нового поставщика фильтрация является частью самой платформы ведения журнала. Он не выполняется поставщиком Application Insights. Поэтому все фильтры, предоставляемые с помощью перегрузок *илогжерфактори. аддаппликатионинсигхтс ()* , следует удалить. Правила фильтрации и должны быть предоставлены в разделе [Управление инструкциями уровня ведения журнала](#control-logging-level) . Если для фильтрации журналов используется *appSettings. JSON* , он будет продолжать работать с новым поставщиком, так как в обоих случаях используется один и тот же псевдоним поставщика *ApplicationInsights*.

Вы по-прежнему можете использовать старый поставщик. (Он будет удален только при смене основной версии на 3. *XX*) Но мы рекомендуем перейти на новый поставщик по следующим причинам:

- В предыдущем поставщике отсутствует поддержка [областей журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). В новом поставщике свойства из области автоматически добавляются в собранные данные телеметрии в качестве пользовательских свойств.
- Теперь журналы могут быть захвачены в конвейере запуска приложения. Теперь можно записывать журналы из классов **Program** и **Startup** .
- С новым поставщиком фильтрация выполняется на уровне платформы. Вы можете фильтровать журналы в поставщике Application Insights так же, как и для других поставщиков, включая встроенные поставщики, такие как консоль, отладка и т. д. Вы также можете применить те же фильтры к нескольким поставщикам.
- В ASP.NET Core (2,0 и более поздних версиях) рекомендуемый способ [включения регистраторов](https://github.com/aspnet/Announcements/issues/255) — использование методов расширения в илоггингбуилдер в самом **Program.CS** .

> [!Note]
> Новый поставщик доступен для приложений, предназначенных для NETSTANDARD 2.0 или более поздней версии. Если приложение предназначено для более старых версий .NET Core, например .NET Core 1,1, или если оно предназначено для .NET Framework, продолжайте использовать старый поставщик.

## <a name="console-application"></a>Консольное приложение

В следующем коде показан пример консольного приложения, которое настроено для отправки трассировок ILogger в Application Insights.

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

В этом примере используется автономный `Microsoft.Extensions.Logging.ApplicationInsights`пакет. По умолчанию в этой конфигурации для отправки данных в Application Insights используется значение "не меньше нуля". Минимальное значение означает, что InMemoryChannel — это используемый канал. Нет выборки и ни одного стандартного Telemetryinitializer. Это поведение можно переопределить для консольного приложения, как показано в следующем примере.

Установите этот дополнительный пакет:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

В следующем разделе показано, как переопределить Телеметриконфигуратион по умолчанию с помощью **служб. Настройте\<метод > () телеметриконфигуратион** . В этом примере настраивается `ServerTelemetryChannel` и выборке. Он добавляет пользовательский ITelemetryInitializer в Телеметриконфигуратион.

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

## <a name="control-logging-level"></a>Управление уровнем ведения журнала

Технология ASP.NET Core *ILogger* имеет встроенный механизм для применения [фильтрации журналов](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Это позволяет управлять журналами, которые отправляются каждому зарегистрированному поставщику, включая поставщика Application Insights. Фильтрацию можно выполнить либо в конфигурации (обычно с помощью файла *appSettings. JSON* ), либо в коде. Это средство предоставляется самой платформой. Это не относится к поставщику Application Insights.

В следующих примерах правила фильтра применяются к Аппликатионинсигхтслогжерпровидер.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Создание правил фильтрации в конфигурации с помощью appSettings. JSON

Для Аппликатионинсигхтслогжерпровидер псевдонимом поставщика является `ApplicationInsights`. В следующем разделе файла *appSettings. JSON* настраиваются журналы для *предупреждений* и более поздних версий от всех категорий и *ошибок* и выше по категориям, которые начинаются с " `ApplicationInsightsLoggerProvider`Microsoft" для отправки.

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

### <a name="create-filter-rules-in-code"></a>Создание правил фильтрации в коде

Следующий фрагмент кода настраивает журналы на наличие *предупреждений* и более поздних версий от всех категорий, а также для *ошибок* и более поздних версий от категорий, `ApplicationInsightsLoggerProvider`которые начинаются с "Microsoft" для отправки. Эта конфигурация такая же, как в предыдущем разделе в *appSettings. JSON*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Каковы старые и новые версии Аппликатионинсигхтслогжерпровидер?

[Пакет SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) включал встроенный Аппликатионинсигхтслогжерпровидер (Microsoft. ApplicationInsights. AspNetCore. Logging. аппликатионинсигхтслогжерпровидер), который был включен с помощью **илогжерфактори** методы расширения. Этот поставщик помечен как устаревший с версии 2.7.0-2. Он будет полностью удален при следующем изменении основной версии. Пакет [2.6.1 Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) не является устаревшим. Необходимо включить наблюдение за запросами, зависимостями и т. д.

Предложенным альтернативным вариантом является новый автономный пакет [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), который содержит Улучшенный аппликатионинсигхтслогжерпровидер ( Microsoft. Extensions. Logging. ApplicationInsights. Аппликатионинсигхтслогжерпровидер) и методы расширения для Илогжербуилдер, чтобы включить его.

[Пакет SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.7.0-beta3 зависит от нового пакета и автоматически включает запись ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Почему некоторые журналы ILogger показаны дважды в Application Insights?

Дублирование может произойти при наличии более старой (устаревшей) версии аппликатионинсигхтслогжерпровидер с включенным `AddApplicationInsights` вызовом `ILoggerFactory`on. Проверьте, есть ли у метода **настройки** следующие параметры, и удалите его:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Если при отладке из Visual Studio вы настроили двойное ведение `EnableDebugLogger` журнала, задайте для параметра значение *false* в коде, который включает Application Insights, как показано ниже. Это дублирование и исправление применимо только при отладке приложения.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Я обновил [пакет SDK для Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.7.0-beta3, а журналы из ILogger фиксируются автоматически. Разделы справки полностью отключить эту функцию?

Сведения о том, как отфильтровать журналы в целом, см. в разделе [Управление уровнем ведения журнала](../../azure-monitor/app/ilogger.md#control-logging-level) . Чтобы отключить Аппликатионинсигхтслогжерпровидер, используйте `LogLevel.None`:

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

Application Insights записывает и отправляет журналы ILogger, используя те же Телеметриконфигуратион, которые используются для всех остальных данных телеметрии. Но существует исключение. По умолчанию Телеметриконфигуратион не полностью настраивается при регистрации из **Program.CS** или **Startup.CS**. Журналы из этих мест не будут иметь конфигурации по умолчанию, поэтому они не будут выполнять все Telemetryinitializer и TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Я использую автономный пакет Microsoft. Extensions. Logging. ApplicationInsights и хочу записывать некоторые дополнительные пользовательские данные телеметрии вручную. Как это сделать?

При использовании автономного пакета `TelemetryClient` не внедряется в контейнер di, поэтому необходимо создать новый `TelemetryClient` экземпляр и использовать ту же конфигурацию, что и поставщик средства ведения журнала, как показано в следующем коде. Это гарантирует, что одна и та же конфигурация будет использоваться для всех пользовательских данных телеметрии, а также для телеметрии из ILogger.

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
> Если вы используете пакет Microsoft. ApplicationInsights. AspNetCore для включения Application Insights, измените этот код, чтобы получить `TelemetryClient` его непосредственно в конструкторе. Пример см. в разделе [часто задаваемые вопросы](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Какой тип телеметрии Application Insights создан из журналов ILogger? Или где можно просмотреть журналы ILogger в Application Insights?

Аппликатионинсигхтслогжерпровидер записывает журналы ILogger и создает Трацетелеметри из них. Если объект исключения передается в метод **log ()** для ILogger, вместо Трацетелеметри создается *ексцептионтелеметри* . Эти элементы телеметрии можно найти в тех же местах, что и другие Трацетелеметри или Ексцептионтелеметри, для Application Insights, включая портал, аналитику или локальный отладчик Visual Studio.

Если вы предпочитаете всегда отсылать Трацетелеметри, используйте следующий фрагмент кода:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Я не установил пакет SDK, и я использую расширение веб-приложений Azure, чтобы включить Application Insights для приложений ASP.NET Core. Разделы справки использовать новый поставщик? 

Расширение Application Insights в веб-приложениях Azure использует старый поставщик. Правила фильтрации в файле *appSettings. JSON* для приложения можно изменить. Чтобы воспользоваться преимуществами нового поставщика, используйте инструментирование времени сборки, принимая зависимость NuGet от пакета SDK. Эта статья будет обновлена при переключении расширения на использование нового поставщика.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Я использую автономный пакет Microsoft. Extensions. Logging. ApplicationInsights и включив поставщик Application Insights путем вызова **построителя. Аддаппликатионинсигхтс ("iKey")** . Есть ли возможность получить ключ инструментирования из конфигурации?


Измените Program.cs и appSettings. JSON следующим образом:

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

Этот код необходим только при использовании автономного поставщика ведения журнала. Для регулярного мониторинга Application Insights ключ инструментирования загружается автоматически из пути *конфигурации ApplicationInsights: Instrumentationkey*. AppSettings. JSON должен выглядеть следующим образом:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Следующие шаги

См. также:

* [Вход в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Журналы трассировки .NET в Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
