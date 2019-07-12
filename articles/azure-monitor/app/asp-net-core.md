---
title: Azure Application Insights для приложений ASP.NET Core | Документация Майкрософт
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
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827808"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights для приложений ASP.NET Core

В этой статье описывается включение Application Insights для [ASP.NET Core](https://docs.microsoft.com/aspnet/core) приложения. Когда вы выполните инструкции в этой статье, Application Insights будет собирать запросы, зависимости, исключения, счетчики производительности, периодических сигналов и журналы из приложения ASP.NET Core. 

В примере, мы используем здесь представлена [приложения MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) излюбленного `netcoreapp2.2`. Эти инструкции можно применить ко всем приложениям ASP.NET Core.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

[Пакет SDK Application Insights для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) можно отслеживать работу приложений, независимо от того, откуда и как они выполняются. Если приложение работает и подключен к сети Azure, сбор данных телеметрии. Везде, где поддерживается .NET Core поддерживается мониторинга Application Insights. Описание поддержки:
* **Операционная система**. Windows, Linux или Mac.
* **Размещение метод**: В процессе или вне процесса. 
* **Метод развертывания**: Платформа зависимые или автономное.
* **Веб-сервер**: Службы IIS (Internet Information Server) или Kestrel. 
* **Платформа размещения**: Компонент веб-приложений службы приложений Azure, виртуальных Машин Azure, Docker, Azure Kubernetes Service (AKS) и т. д.
* **ИНТЕГРИРОВАННАЯ СРЕДА РАЗРАБОТКИ**: Visual Studio, Visual STUDIO Code или командной строки.

## <a name="prerequisites"></a>предварительные требования

- Функционирующее приложение ASP.NET Core. Если вам нужно создать приложение ASP.NET Core, выполните это [учебник по ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Допустимый ключ инструментирования Application Insights. Этот ключ требуется отправить данные телеметрии в Application Insights. Если вам нужно создать новый ресурс Application Insights для получения ключа инструментирования-см [создайте ресурс Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Включить серверные данные телеметрии Application Insights (Visual Studio)

1. Откройте проект в Visual Studio.

    > [!TIP]
    > Если вы хотите, можно выполнить настройку системы управления версиями для проекта, можно отслеживать все изменения, сделанные в Application Insights. Чтобы включить систему управления версиями, выберите **файл** > **добавить в систему управления версиями**.

2. Выберите **Проект** > **Добавить телеметрию Application Insights**.

3. Выберите **Начать**. Этот выбор текста может отличаться в зависимости от вашей версии Visual Studio. Использовать более ранние версии **начать бесплатно** вместо кнопки.

4. Выберите свою подписку. Затем выберите **ресурсов** > **зарегистрировать**.

5. После добавления Application Insights в проект, установите флажок, чтобы подтвердить, что вы используете последнего стабильного выпуска пакета SDK. Перейдите к **проекта** > **управление пакетами NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Если необходимо, выберите **обновления**.

     ![Снимок экрана, показывающий, где можно выбрать пакет Application Insights для обновления](./media/asp-net-core/update-nuget-package.png)

6. Если вы выполните дополнительные подсказки и добавить проект в систему управления версиями, перейдите к **представление** > **Team Explorer** > **изменения**. Выберите каждый файл, чтобы увидеть разностное представление изменения, внесенные программой телеметрии Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Включить серверные данные телеметрии Application Insights (не Visual Studio)

1. Установка [пакет NuGet пакета SDK для Application Insights для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Мы рекомендуем всегда использовать последнюю стабильную версию. Найти заметки о полном выпуске для пакета SDK на [открытый репозиторий GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    В следующем примере кода показаны изменения для добавления в проект `.csproj` файл.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Добавить `services.AddApplicationInsightsTelemetry();` для `ConfigureServices()` метод в вашей `Startup` класса, как в следующем примере:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Настройте ключ инструментирования.

    Несмотря на то, что ключ инструментирования можно ввести в качестве аргумента `AddApplicationInsightsTelemetry`, рекомендуется задать ключ инструментирования в конфигурации. В следующем образце кода показано, как задать ключ инструментирования в `appsettings.json`. Убедитесь, что `appsettings.json` копируется в корневую папку приложения во время публикации.

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

    Кроме того можно укажите ключ инструментирования в одном из следующих переменных среды:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Пример:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Как правило `APPINSIGHTS_INSTRUMENTATIONKEY` указывает ключ инструментирования для приложений, развернутых в веб-приложения.

    > [!NOTE]
    > Ключ инструментирования, указанный в wins кода, чем переменная среды `APPINSIGHTS_INSTRUMENTATIONKEY`, который разрешается в пользу другими вариантами.

## <a name="run-your-application"></a>Запуск приложения

Запустите приложение и выполнять запросы к нему. Теперь поток данных телеметрии в Application Insights. Пакет SDK Application Insights автоматически собирает следующие данные телеметрии.

|Запросы и зависимости |Сведения|
|---------------|-------|
|Requests | Входящие веб-запросы к приложению. |
|HTTP или HTTPS | Вызовы, сделанные с помощью `HttpClient`. |
|SQL | Вызовы, сделанные с помощью `SqlClient`. |
|[Хранилище Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Вызовы, сделанные с помощью клиента службы хранилища Azure. |
|[Пакет SDK для клиента концентраторов событий](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Версии 1.1.0 и более поздних версий. |
|[Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Версии 3.0.0 и более поздних версий. |
|Azure Cosmos DB | Отслеживаются автоматически, только в том случае, если используется HTTP/HTTPS. Application Insights не захватывает режим TCP. |

### <a name="performance-counters"></a>Счетчики производительности

Поддержка [счетчики производительности](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) в ASP.NET Core ограничено:

   * Пакет SDK версии 2.4.1 и более поздней версии собирать счетчики производительности, если приложение выполняется в веб-приложений (Windows).
   * 2\.7.0-beta3 версии пакета SDK и более поздней версии собирать счетчики производительности, если приложение выполняется в Windows и целевые объекты `NETSTANDARD2.0` или более поздней версии.
   * Для приложений, предназначенных для .NET Framework все версии пакета SDK поддерживает счетчики производительности.
 
В этой статье будет обновляться при добавлении поддержки счетчика производительности в Linux.

### <a name="ilogger-logs"></a>Журналы ILogger

[Журналы ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) серьезности `Warning` или более поздней версии автоматически регистрируются в 2.7.0-beta3 версии пакета SDK и более поздних версий.

### <a name="live-metrics"></a>Live Metrics

Может занять несколько минут, прежде чем телеметрии начинает появляться на портале. Чтобы быстро убедиться в надлежащей работе всех компонентов, лучше всего использовать [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) при выполнения запросов к запущенному приложению.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Включение телеметрии на стороне клиента для веб-приложений

Описанные выше шаги являются достаточно, чтобы помочь вам начать сбор данных телеметрии на стороне сервера. Если приложение состоит из компонентов на стороне клиента, выполните описанные ниже действия, чтобы начать сбор [данные телеметрии их использования](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. В `_ViewImports.cshtml`, добавьте путем внедрения кода:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. В `_Layout.cshtml`, вставить `HtmlHelper` в конце `<head>` раздела, но перед любым другим скриптом. Если вы хотите сообщить о любой пользовательской телеметрии JavaScript на странице, внедрить его после этот фрагмент кода:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` Имена файлов, упомянутый ранее, с помощью шаблона приложения MVC по умолчанию. В конечном счете, если вы хотите обеспечить наблюдения на стороне клиента для приложения, фрагмент JavaScript должен указываться в `<head>` части каждой страницы приложения, которое вы хотите отслеживать. Эта цель для этого шаблона приложения можно сделать, добавив фрагмент JavaScript для `_Layout.cshtml`. 

Если проект не содержит `_Layout.cshtml`, вы сможете добавлять [наблюдения на стороне клиента](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Это можно сделать, добавив фрагмент кода JavaScript, управляющий эквивалентного файла `<head>` всех страниц в приложении. Или можно добавить фрагмент кода на несколько страниц, но это решение является трудным в обслуживании и обычно не рекомендуется.

## <a name="configure-the-application-insights-sdk"></a>Настройка пакета SDK Application Insights

Вы можете настроить пакет SDK Application Insights для ASP.NET Core изменить конфигурацию по умолчанию. Пользователей из пакета SDK Application Insights ASP.NET может быть знакомы с изменения конфигурации с помощью `ApplicationInsights.config` или путем изменения `TelemetryConfiguration.Active`. Можно изменить конфигурацию по-разному для ASP.NET Core. Добавьте пакет SDK для ASP.NET Core в приложение и настройте его с помощью ASP.NET Core встроенные [внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Почти все изменения конфигурации в `ConfigureServices()` метод вашей `Startup.cs` класса, если только вы будете направлены в противном случае. В следующих разделах приведены дополнительные сведения.

> [!NOTE]
> В приложениях ASP.NET Core, изменение конфигурации путем изменения `TelemetryConfiguration.Active` не поддерживается.

### <a name="using-applicationinsightsserviceoptions"></a>С помощью ApplicationInsightsServiceOptions

Имеет ряд общих параметров можно изменить путем передачи `ApplicationInsightsServiceOptions` для `AddApplicationInsightsTelemetry`, как показано в этом примере:

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

Дополнительные сведения см. в разделе [настраиваемые параметры `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Выборка

Пакет SDK Application Insights для ASP.NET Core поддерживает фиксированной частотой и Адаптивная выборка. Адаптивная выборка включена по умолчанию. 

Дополнительные сведения см. в разделе [Настройка адаптивной выборки для приложений ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Добавление свойство TelemetryInitializers

Используйте [инициализаторы телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) при необходимости определения глобальных свойств, которые отправляются со всеми данными телеметрии.

Добавляйте новые `TelemetryInitializer` для `DependencyInjection` контейнера, как показано в следующем коде. Пакет SDK автоматически выбирает копирования любых `TelemetryInitializer` добавляется к `DependencyInjection` контейнера.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Удаление свойство TelemetryInitializers

Инициализаторы телеметрии присутствуют по умолчанию. Чтобы удалить все или инициализаторы определенной телеметрии, используйте приведенный ниже пример *после* вызове `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

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

Вы можете добавить пользовательские данные телеметрии процессоров `TelemetryConfiguration` с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` на `IServiceCollection`. Использовать обработчики данных телеметрии в [Расширенная фильтрация сценариев](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) позволяющие непосредственно контролировать то, что включены или исключены из данных телеметрии, отправляемые в службу Application Insights. Используйте следующий пример.

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

Application Insights использует модули телеметрии для [автоматически собирают полезные сведения](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) о конкретных рабочих нагрузок без необходимости дополнительной настройки.

По умолчанию включены следующие модули коллекции автоматически. Эти модули отвечают за автоматически собирает данные телеметрии. Можно отключить или настроить их для изменения их поведения по умолчанию.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Чтобы настроить любое по умолчанию `TelemetryModule`, использовать метод расширения `ConfigureTelemetryModule<T>` на `IServiceCollection`, как показано в следующем примере.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

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

### <a name="configuring-a-telemetry-channel"></a>Настройка канал телеметрии

Канал по умолчанию является `ServerTelemetryChannel`. Его можно переопределить, как показано в следующем примере.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Отключить сбор данных телеметрии динамически

Если вы хотите отключить сбор данных телеметрии по условию и динамически, можно решить `TelemetryConfiguration` экземпляра с помощью контейнера внедрения зависимостей ASP.NET Core в любом месте кода и задайте `DisableTelemetry` флаг на нем.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Как можно отслеживать данные телеметрии, собираемые автоматически?

Получить экземпляр `TelemetryClient` , используя конструктор injection и вызывать необходимые `TrackXXX()` для него метода. Мы не рекомендуем создание новых `TelemetryClient` экземпляров в приложении ASP.NET Core. Одноэлементный экземпляр `TelemetryClient` уже зарегистрирован в `DependencyInjection` контейнера, который совместно использует `TelemetryConfiguration` с остальной частью данных телеметрии. Создание нового `TelemetryClient` экземпляра рекомендуется только если это необходимо в конфигурации, который отделен от остальной части данных телеметрии. 

В следующем примере показано, как отслеживать дополнительные данные телеметрии из контроллера.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Дополнительные сведения о пользовательских данных, создания отчетов в Application Insights, см. в разделе [пользовательских метрик Application Insights Справочник по API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Некоторые шаблоны Visual Studio используется метод расширения UseApplicationInsights() на IWebHostBuilder для включения Application Insights. Такое использование по-прежнему действительна?

Да, включить Application Insights с помощью этого метода является допустимым. Этот метод используется в адаптации Visual Studio и в расширениях веб-приложений. Тем не менее, мы рекомендуем использовать `services.AddApplicationInsightsTelemetry()` предполагающий перегрузки для управления определенной настройки. Оба метода сделать то же самое на внутреннем уровне, поэтому если не нужно применять пользовательскую конфигурацию, можно вызвать либо метод.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Мое приложение ASP.NET Core в веб-приложения выполняется развертывание. Следует по-прежнему включать расширение Application Insights из веб-приложений?

Если пакет SDK установлен во время сборки, как показано в этой статье, вам не нужно включать расширение Application Insights на портале службы приложений. Даже если расширение установлено, оно будет пассивный режим когда обнаруживает, что пакет SDK уже добавлен в приложение. При включении Application Insights из модуля, не нужно устанавливать и обновлять пакет SDK. Но при включении Application Insights, следуя указаниям в этой статье, вам доступно больше вариантов, так как:

   * Телеметрия Application Insights будут продолжать работать:
       * Все операционные системы, включая Windows, Linux и Mac.
       * Все публикации режимов, включая автономное или зависимые framework.
       * Часы всех целевых платформ, включая полную версию .NET Framework.
       * Варианты с все размещения, включая веб-приложений, виртуальных машин, Linux, контейнеры, службе Azure Kubernetes и размещение не Azure.
   * Вы можете просматривать данные телеметрии локально, при отладке из Visual Studio.
   * Вы можете отслеживать дополнительные пользовательские телеметрические данные с помощью `TrackXXX()` API.
   * У вас есть полный контроль над конфигурацией.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Включить мониторинг Application Insights с помощью таких средств, как монитор состояния?

Нет. [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и [v2 монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) в настоящее время поддерживает ASP.NET 4.x только.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights автоматически включается для моего приложения ASP.NET Core 2.0?

`Microsoft.AspNetCore.All` 2.0 метапакет включен пакет SDK Application Insights (версия 2.1.0). При запуске приложения в отладчике Visual Studio, Visual Studio позволяет Application Insights и отображаются данные телеметрии, локально в самой интегрированной среды разработки. Данные телеметрии не был отправлен в службу Application Insights, если не указан ключ инструментирования. Мы рекомендуем следовать инструкциям в этой статье, как включить Application Insights, даже для 2.0 приложения.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>При запуске приложения в Linux, все функции поддерживаются?

Да. Поддержка функций пакета SDK для одинаков на всех платформах, за исключением следующих случаев:

* Счетчики производительности, поддерживаются только в Windows.
* Несмотря на то что `ServerTelemetryChannel` включена по умолчанию, если приложение выполняется в Linux или MacOS, канал не создает автоматически папку локального хранилища для хранения данных телеметрии временно, при наличии проблемы с сетью. Данные телеметрии из-за этого ограничения, теряются при возникновении временных неполадок сети или сервера. Чтобы обойти эту проблему, настройте локальную папку для канала:

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
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Видео

- Ознакомьтесь с этой внешней пошаговые видео, чтобы [настроить Application Insights с помощью .NET Core и Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) с нуля.
- Ознакомьтесь с этой внешней пошаговые видео, чтобы [настроить Application Insights с помощью .NET Core и Visual Studio Code](https://youtu.be/ygGt84GDync) с нуля.

## <a name="next-steps"></a>Следующие шаги

* [Изучите маршруты пользователей](../../azure-monitor/app/usage-flows.md) чтобы понять, как пользователи перемещаются приложением.
* [Настройка сбора моментальных снимков](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) для просмотра состояния исходного кода и переменных в момент, когда возникает исключение.
* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для подробное представление производительности и использования вашего приложения.
* Используйте [тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md), чтобы постоянно проверять работу приложения из всех точек мира.
* [Внедрение зависимостей в ASP.NET Core](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
