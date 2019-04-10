---
title: Azure Application Insights для ASP.NET Core без Visual Studio | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений ASP.NET Core.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288371"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights для приложений ASP.NET Core

В этой статье описаны действия по настройке Application Insights для [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) приложения без использования Visual Studio IDE. Если у вас есть Visual Studio IDE, переустанавливать [это](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) документ содержит инструкции для Visual Studio. Выполнив инструкции в этой статье, Application Insights начнет собирать запросы, зависимости, исключения, счетчики производительности, периодических сигналов и журналы из приложения ASP.NET Core. — Пример приложения, используемого [приложения MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) нацеливания `netcoreapp2.2`, но здесь инструкции применимы ко всем приложениям ASP.NET Core. Любые исключения, называются, если это применимо.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Пакет SDK Application Insights (пакет средств разработки программного обеспечения) для ASP.NET Core можно отслеживать приложения независимо от того, откуда и как его запуске. Если приложение работает и имеет сетевое подключение к службе Application Insights, собираемых ожидается телеметрии. Эта поддержка включает в себя, но не ограничивается любой операционной системы (Windows, Linux, Mac), метод размещения (vs в процессе ожидания процесса), метод развертывания (зависящего от платформы Visual Studio автономное), веб-сервер (IIS, Kestrel), платформы (веб-приложений Azure, виртуальных Машин в Azure Docker, AKS и т. д.) или интегрированной среде разработки (Командная строка Visual Studio, VS Code).

## <a name="prerequisites"></a>Необходимые компоненты

- Работающее приложение ASP.NET Core. Выполните [это](https://docs.microsoft.com/aspnet/core/getting-started/) руководство по созданию приложения ASP.NET Core, при необходимости.
- Допустимый ключ инструментирования Application Insights, который требуется отправить данные телеметрии в службу Application Insights. Выполните [эти](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) инструкции по созданию нового ресурса Application Insights, при необходимости и получение ключа инструментирования.

## <a name="enabling-application-insights-server-side-telemetry"></a>Включение телеметрии Application Insights на сервере

1. Установите пакет SDK Application Insights для ASP.NET Core, который является обычный пакет NuGet. Рекомендуется всегда использовать последнюю стабильную версию. Некоторые функции, описанные в этой статье не доступны в предыдущих версиях. Заметки о полном выпуске для пакета SDK можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

Ниже показаны изменения для добавления проекта CSPROJ-файл.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Добавить `services.AddApplicationInsightsTelemetry();` для `ConfigureServices()` метод в вашей `Startup` класса. Полный пример ниже.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. Настройте ключ инструментирования.

   Хотя можно предоставить ключ инструментирования в качестве аргумента `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, рекомендуется задать ключ инструментирования в конфигурации. Ниже показано, как задать ключ инструментирования в `appsettings.json`. Убедитесь, что `appsettings.json` копируется в корневую папку приложения во время публикации.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` обычно используется для указания ключа инструментирования для приложений, развернутых в Azure веб-приложения.

> [!NOTE]
> Ключ инструментирования, указанный в wins кода, чем переменная среды `APPINSIGHTS_INSTRUMENTATIONKEY`, который разрешается в пользу другими вариантами.

4. Запустите приложение и выполнять запросы к нему. Данные телеметрии теперь начнут поступать в Application Insights. Пакет SDK Application Insights автоматически собирает следующие данные телеметрии.

    1. Запросы - входящего веб-запросов приложения.
    1. Зависимости
        1. Вызовы HTTP/Https с `HttpClient`
        1. SQL-вызовы, сделанные с помощью `SqlClient`
        1. Вызовы к службе хранилища Azure с [клиента хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [Пакет SDK для клиента концентратора событий](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) версии 1.1.0 и более поздних версий
        1. [Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) версии 3.0.0 и выше

             * Azure Cosmos DB автоматически отслеживаются только в том случае, если используется HTTP/HTTPS. Режим TCP не будет отслеживаться с помощью Application Insights.


    1. [Счетчики производительности](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Для счетчиков производительности в ASP.NET Core поддерживается только для следующих
            1. Пакет SDK версии 2.4.1 и выше собирает счетчики производительности, если приложение выполняется в веб-приложения Azure (Windows)
            1. Пакет SDK версии 2.7.0-beta3 и выше собирает счетчики производительности, если приложение работает в Windows и предназначенных для `NETSTANDARD2.0` или более поздней версии.
            1. Приложение предназначено для полной версии платформы .NET Framework счетчики производительности поддерживаются во всех версиях пакета SDK.

            В этом документе будет обновляться при добавлении поддержки счетчика производительности в Linux.
    1. [Интерактивные метрики](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` журналы уровня серьезности `Warning` или выше, автоматически зарегистрированные из пакета SDK версии 2.7.0-beta3 или более поздней версии. Дополнительные сведения см. [здесь](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Может занять несколько минут для телеметрии до начала отображения на портале. Чтобы быстро узнать, если все работает, то лучше использовать [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), тогда как при отправке запросов к запущенному приложению.

## <a name="send-ilogger-logs-to-application-insights"></a>Отправка журналов ILogger Application Insights

Application Insights поддерживает записи журналов, отправляемых через ILogger. Ознакомьтесь с документацией по полной [здесь](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Включение телеметрии на стороне клиента для веб-приложений

Описанные выше действия, достаточно для начала сбора данных телеметрии на стороне сервера. Если приложение состоит из компонентов на стороне клиента, выполните следующие действия, чтобы начать сбор [данные телеметрии их использования](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) оттуда.

1. В _ViewImports.cshtml добавьте путем внедрения кода:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. В _Layout.cshtml, вставьте HtmlHelper в конец `<head>` раздела, но перед любым другим скриптом. Все пользовательские данные телеметрии JavaScript, которые следует включить в отчет на странице должно быть внедрено после этот фрагмент кода:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Измените имена файлов, в соответствии с ваше фактическое приложение. Выше, называются на основе шаблона приложения MVC по умолчанию.

## <a name="configuring-application-insights-sdk"></a>Настройка пакета SDK Application Insights

Пакет SDK Application Insights для ASP.NET Core можно настроить на изменение конфигурации по умолчанию. Пользователи из пакета SDK Application Insights ASP.NET может быть знакомы с использованием конфигурации `ApplicationInsights.config`, или путем изменения `TelemetryConfiguration.Active`. Для ASP.NET Core Настройка выполняется по-разному. Пакет SDK для ASP.NET Core добавляется в приложение с помощью ASP.NET Core встроенные [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) механизм и настройка же также с помощью DependencyInjection. Почти все изменения конфигурации выполняются в `ConfigureServices()` метод вашей `Startup.cs` класса, если не указано иное. Выполните Дополнительные сведения в следующих разделах.

> [!NOTE]
> Это важно отметить, что изменение конфигурации, изменяя `TelemetryConfiguration.Active` не рекомендуется в приложениях ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Настройка с помощью ApplicationInsightsServiceOptions

Можно изменить имеет ряд общих параметров, передав `ApplicationInsightsServiceOptions` для `services.AddApplicationInsightsTelemetry();`. Ниже приведен пример такого файла.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Точный список настраиваемых параметров в `ApplicationInsightsServiceOptions` можно найти [здесь](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Выборка

Пакет SDK Application Insights для ASP.NET Core поддерживает FixedRate и адаптивной выборки. Адаптивная выборка включена по умолчанию. Выполните [это](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) документа, чтобы узнать, как настроить выборки для приложений ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Добавление свойство TelemetryInitializers

Чтобы добавить новую `TelemetryInitializer`, добавьте его в контейнер DependencyInjection, как показано ниже. `TelemetryInitializer`s, добавлении DependencyInjection контейнер будет с помощью пакета SDK автоматически выбирает.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Удаление свойство TelemetryInitializers

Для удаления всех или определенных свойство TelemetryInitializers, которые присутствуют по умолчанию, используйте приведенный ниже пример **после** вызова `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Добавление TelemetryProcessors

Обработчики пользовательских данных телеметрии, которые могут добавляться к `TelemetryConfiguration` с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` на `IServiceCollection`. Используйте следующий пример.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Настройка или удаление TelemetryModules по умолчанию

Следующие модули коллекции автоматически включены по умолчанию, а также отвечают за автоматически собирает данные телеметрии. Их можно отключить и настроить для изменения поведения по умолчанию.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Чтобы настроить любое по умолчанию `TelemetryModule`, использовать метод расширения `ConfigureTelemetryModule<T>` на `IServiceCollection` как показано в следующем примере.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Настройка канал телеметрии

— Канал по умолчанию, используемый `ServerTelemetryChannel`. Его можно переопределить в следующем примере ниже.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

*1. Требуется отслеживать некоторые дополнительные данные телеметрии, помимо данные телеметрии собираются автоматически. Как это сделать?*

* Получить экземпляр `TelemetryClient` , используя конструктор injection и вызывать необходимые `TrackXXX()` для него метода. Не рекомендуется для создания новых `TelemetryClient` экземпляров в приложении ASP.NET Core, как одноэлементный экземпляр `TelemetryClient` уже зарегистрирован в контейнер с внедрением Зависимостей, которая использует `TelemetryConfiguration` с остальной частью данных телеметрии. Создание нового `TelemetryClient` экземпляра рекомендуется только в том случае, если он должен иметь отдельные конфигурации от остальной части данных телеметрии. В следующем примере показано, как отслеживать дополнительные данные телеметрии из контроллера.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Ссылаться на [пользовательских метрик Application Insights Справочник по API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) описание пользовательских данных, создания отчетов в Application Insights.

*2. Некоторые шаблоны Visual Studio используется метод расширения UseApplicationInsights() на IWebHostBuilder для включения Application Insights. Такое использование по-прежнему действительна?*

* Включение Application Insights с помощью этого метода является допустимым и используется в Visual Studio адаптации, а также в расширениях веб-приложения Azure также. Тем не менее, рекомендуется использовать `services.AddApplicationInsightsTelemery()` так как он обеспечивает перегрузки для управления определенной настройки. Оба метода внутренне делает то же самое, таким образом, если нет пользовательские конфигурации для применения, вызов либо подходит.

*3. Я выполняю развертывание Мое приложение ASP.NET Core, веб-приложения Azure. Следует по-прежнему включать расширение Application Insights из веб-приложений?*

* Если пакет SDK установлен во время сборки, как показано в этой статье, нет необходимости включить расширение Application Insights на портале веб-приложений. Даже если расширение установлено, оно будет отсрочки, когда обнаруживает, что пакет SDK уже добавлен в приложение. Включение Application Insights из расширения освобождает вас от установки и обновления пакета SDK в приложение. Тем не менее Включение Application Insights в соответствии с этой статьи является более гибким по причинам ниже.
    1. Телеметрия Application Insights будут работать
        1. Все операционные системы — Windows, Linux, Mac.
        1. Все публикации режим — автономные или зависящего от платформы.
        1. Часы всех целевых платформ, включая полную версию .NET Framework.
        1. Все параметры размещения - веб-приложения Azure, виртуальные машины, Linux, контейнеры, AKS, не относящиеся к Azure.
    1. Данные телеметрии можно увидеть локально, при отладке из Visual Studio.
    1. Позволяет использовать дополнительные пользовательские телеметрические данные отслеживания `TrackXXX()` API.
    1. Имеет полный контроль над конфигурацией.

*4. Включить мониторинг Application Insights с помощью таких средств, как монитор состояния?*

* Нет. [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и их замене предстоящих [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) сейчас поддерживает только ASP.NET. Документ будет обновлен при поддерживает приложения ASP.NET Core.

*5. У меня есть приложение ASP.NET Core 2.0? Не Application Insights включено автоматически для них без мне делать?*

* `Microsoft.AspNetCore.All` метапакет 2.0 включены в пакет SDK Application Insights (версия 2.1.0), и при запуске приложения в отладчике Visual Studio, Visual Studio позволяет application insights и отображаются данные телеметрии, локально в самой интегрированной среды разработки. Данные телеметрии не было отправлено в службу Application Insights, если не задан явно ключ инструментирования. Мы рекомендуем следуйте инструкциям в этой статье, чтобы включить Application Insights, даже для 2.0 приложения.

*6. Запустим Мое приложение в Linux. Все функции в Linux также поддерживаются?*

* Да. Поддержка функций пакета SDK для совпадает на всех платформах, за исключением следующих случаев:
    1. Счетчики производительности еще не поддерживаются в не-Windows. В этом документе будет обновляться при добавлении поддержки Linux.
    1. Несмотря на то что `ServerTelemetryChannel` включена по умолчанию, если приложение выполняется в отличных от windows, канал не создает автоматически папку локального хранилища для хранения данных телеметрии временно, при наличии проблемы с сетью. Это ограничение приводит к телеметрии будут потеряны, если временные сети или проблемы сервера. Возможным решением этой проблемы является для пользователя настроить локальную папку для канала, как показано ниже.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
