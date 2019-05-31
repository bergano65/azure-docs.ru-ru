---
title: Azure Application Insights для ASP.NET Core | Документация Майкрософт
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
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226402"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights для приложений ASP.NET Core

В этой статье описывается включение Application Insights для [ASP.NET Core](https://docs.microsoft.com/aspnet/core) приложения. Когда вы выполните инструкции в этой статье, Application Insights начнет собирать запросы, зависимости, исключения, счетчики производительности, периодических сигналов и журналы из приложения ASP.NET Core. Пример приложения представляет [приложения MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) нацеливания `netcoreapp2.2`, но эти инструкции применимы ко всем приложениям ASP.NET Core.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

[Application Insights SDK (пакет средств разработки программного обеспечения) для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) можно отслеживать работу приложений, независимо от того, откуда и как его запуске. Если приложение работает и подключен к сети Azure, сбор данных телеметрии. Это означает, что мониторинг Application Insights поддерживается везде, где поддерживается .NET Core. Эта поддержка включает любой операционной системы (Windows, Linux, Mac), метод размещения (vs в процессе ожидания процесса), метод развертывания (зависящего от платформы Visual Studio автономное), веб-сервер (IIS, Kestrel), платформа размещения (веб-приложений Azure, виртуальных Машин Azure, Docker, программы Служба Azure Kubernetes (AKS) и т. д.) или интегрированной среде разработки (Командная строка Visual Studio, VS Code).

## <a name="prerequisites"></a>Технические условия

- Работающее приложение ASP.NET Core. Выполните [руководство по ASP.NET Core начало](https://docs.microsoft.com/aspnet/core/getting-started/) для создания приложения ASP.NET Core, при необходимости.
- Допустимый ключ инструментирования Application Insights, который требуется отправить данные телеметрии в службу Application Insights. Выполните [создать ресурс инструкции](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) для создания нового ресурса Application Insights, при необходимости и получение ключа инструментирования.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Включить серверные данные телеметрии Application Insights (Visual Studio)

1. Откройте проект в Visual Studio.

    > [!TIP]
    > Во время не является обязательной может быть полезным для настройки системы управления версиями для проекта, поэтому вы сможете отслеживать все изменения, внесенные службой Application Insights. Для выбора элемента управления источника **файл** > **добавить в систему управления версиями**.

2. Выберите **Проект** > **Добавить телеметрию Application Insights**.

3. Выберите **Начать**. (В зависимости от используемой версии Visual Studio текст может несколько отличаться. У некоторых предыдущих версий есть кнопка **Начать бесплатно**.)

4. Выберите свою подписку, а затем выберите **Ресурс** > **Зарегистрировать**.

5. После добавления Application Insights в проект, установите флажок, чтобы подтвердить, что вы используете последнего стабильного выпуска пакета SDK. Перейдите к **проекта** > **управление пакетами NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > при необходимости выберите **Обновления**.

     ![Снимок экрана: управление экрана пакета NuGet с помощью пакета Application Insights, выбранные для обновления](./media/asp-net-core/update-nuget-package.png)

6. Если за необязательным tip и добавить проект в систему управления версиями можно затем перейти к **представление** > **Team Explorer** > **изменения** и Выберите каждый отдельный файл для разностное представление изменений, сделанных добавления телеметрии Application Insights.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Включить серверные данные телеметрии в Application Insights (без Visual Studio)

1. Установка [пакет NuGet пакета SDK для Application Insights для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Мы рекомендуем всегда использовать последнюю стабильную версию. Заметки о полном выпуске для пакета SDK можно найти на [откройте репозиторий GitHub источника](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    В следующем фрагменте показано, чтобы добавить в проект изменения `.csproj` файл.

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

    Хотя можно предоставить ключ инструментирования в качестве аргумента `AddApplicationInsightsTelemetry`, рекомендуется задать ключ инструментирования в конфигурации. Ниже показано, как задать ключ инструментирования в `appsettings.json`. Убедитесь, что `appsettings.json` копируется в корневую папку приложения во время публикации.

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

    Кроме того его также можно указать одним из следующих переменных среды.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Пример:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` обычно используется для указания ключа инструментирования для приложений, развернутых в Azure веб-приложения.

    > [!NOTE]
    > Ключ инструментирования, указанный в wins кода, чем переменная среды `APPINSIGHTS_INSTRUMENTATIONKEY`, который разрешается в пользу другими вариантами.

## <a name="run-your-application"></a>Запуск приложения

 Запустите приложение и выполнять запросы к нему. Данные телеметрии теперь начнут поступать в Application Insights. Пакет SDK Application Insights автоматически собирает следующие данные телеметрии.

|Запросы и зависимости |Сведения|
|---------------|-------|
|Requests | Входящие веб-запросы к приложению. |
|HTTP/Https | Вызовы, сделанные с помощью `HttpClient`. |
|SQL | Вызовы, сделанные с помощью `SqlClient`. |
|[Служба хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Вызовы, сделанные с помощью клиента хранилища Azure. |
|[Пакет SDK для клиента концентратора событий.](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Версии 1.1.0 и более поздних версий. |
|[Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Версии 3.0.0 и выше. |
|Azure Cosmos DB | Отслеживаются автоматически только, если используется HTTP/HTTPS. Режим TCP не будет отслеживаться с помощью Application Insights. |

### <a name="performance-counters"></a>Счетчики производительности

Поддержка [счетчики производительности](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) в ASP.NET Core ограничено следующим

   * Пакет SDK версии 2.4.1 и выше собирает счетчики производительности, если приложение выполняется в веб-приложения Azure (Windows)
   * Пакет SDK версии 2.7.0-beta3 и выше собирает счетчики производительности, если приложение работает в Windows и предназначенных для `NETSTANDARD2.0` или более поздней версии.
   * Для приложений, предназначенных для .NET Framework счетчики производительности поддерживаются во всех версиях пакета SDK.
   * В этой статье будет обновляться при добавлении поддержки счетчика производительности в Linux.

### <a name="ilogger-logs"></a>Журналы ILogger

[Журналы ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) серьезности `Warning` или выше, автоматически зарегистрированные из пакета SDK версии 2.7.0-beta3 или более поздней версии.

### <a name="live-metrics"></a>Live Metrics

Может занять несколько минут для телеметрии до начала отображения на портале. Чтобы быстро узнать, если все работает, то лучше использовать [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), тогда как при отправке запросов к запущенному приложению.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Включение телеметрии на стороне клиента для веб-приложений

Описанные выше действия, достаточно начать сбор данных телеметрии на стороне сервера. Если приложение состоит из компонентов на стороне клиента, выполните следующие действия, чтобы начать сбор [данные телеметрии их использования](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) оттуда.

1. В `_ViewImports.cshtml`, добавьте путем внедрения кода:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. В `_Layout.cshtml`, вставить HtmlHelper в конец `<head>` раздела, но перед любым другим скриптом. Все пользовательские данные телеметрии JavaScript, которые следует включить в отчет на странице должно быть внедрено после этот фрагмент кода:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml` Имена файлов, упомянутые выше, с помощью шаблона приложения MVC по умолчанию. В конечном счете, чтобы обеспечить мониторинг на стороне клиента для приложения требуется фрагмент кода JavaScript, должны присутствовать в `<head>` части каждой страницы приложения, которое вы хотите отслеживать. Для этого приложения шаблона, добавив фрагмент Javascript для `_Layout.cshtml` будет эффективного достижения этой цели. Если проект не имеет этого файла можно добавить [наблюдения на стороне клиента](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Для этого просто необходимо либо добавить JavaScript эквивалентного файла, который управляет `<head>` всех страниц в приложении, или можно также добавить фрагмент кода, чтобы несколько отдельных страниц на то, что это будет трудно поддерживать и не Рекомендуется использовать.

## <a name="configuring-application-insights-sdk"></a>Настройка пакета SDK Application Insights

Пакет SDK Application Insights для ASP.NET Core можно настроить на изменение конфигурации по умолчанию. Пользователи из пакета SDK Application Insights ASP.NET может быть знакомы с использованием конфигурации `ApplicationInsights.config`, или путем изменения `TelemetryConfiguration.Active`. Для ASP.NET Core Настройка выполняется по-разному. Пакет SDK для ASP.NET Core добавляется в приложение и настраивается с помощью ASP.NET Core встроенные [внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Почти все изменения конфигурации выполняются в `ConfigureServices()` метод вашей `Startup.cs` класса, если не указано иное. Выполните Дополнительные сведения в следующих разделах.

> [!NOTE]
>  Идет изменение конфигурации, изменяя `TelemetryConfiguration.Active` не рекомендуется в приложениях ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Настройка с помощью ApplicationInsightsServiceOptions

Можно изменить имеет ряд общих параметров, передав `ApplicationInsightsServiceOptions` для `AddApplicationInsightsTelemetry`. Ниже приведен пример такого файла.

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

Точный список настраиваемых параметров в `ApplicationInsightsServiceOptions` можно найти [здесь](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Выборка

Пакет SDK Application Insights для ASP.NET Core поддерживает FixedRate и адаптивной выборки. Адаптивная выборка включена по умолчанию. Выполните наши [руководство по Адаптивная выборка](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), чтобы узнать, как настроить выборки для приложений ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Добавление свойство TelemetryInitializers

[Инициализаторы телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) используются, когда требуется определить глобальные свойства, которые отправляются со всеми данными телеметрии.

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

Обработчики пользовательских данных телеметрии, которые могут добавляться к `TelemetryConfiguration` с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` на `IServiceCollection`. Обработчики данных телеметрии используются в [Расширенная фильтрация сценариев](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) позволяющие непосредственно контролировать Какова включены или исключены из данных телеметрии, отправляемые в службу Application Insights. Используйте следующий пример.

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

Application Insights использует модули телеметрии как способ [автоматического сбора полезной информации](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) о конкретных рабочих нагрузок, не требуя дополнительной конфигурации.

Следующие модули коллекции автоматически включены по умолчанию, а также отвечают за автоматически собирает данные телеметрии. Их можно отключить и настроить для изменения поведения по умолчанию.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Чтобы настроить любое по умолчанию `TelemetryModule`, использовать метод расширения `ConfigureTelemetryModule<T>` на `IServiceCollection` как показано в следующем примере.

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

### <a name="configuring-telemetry-channel"></a>Настройка канал телеметрии

— Канал по умолчанию, используемый `ServerTelemetryChannel`. Его можно переопределить в следующем примере ниже.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Я хочу отслеживать дополнительные данные телеметрии, отличный от данные телеметрии собираются автоматически. Как это сделать?

Получить экземпляр `TelemetryClient` , используя конструктор injection и вызывать необходимые `TrackXXX()` для него метода. Не рекомендуется для создания новых `TelemetryClient` экземпляров в приложении ASP.NET Core, как одноэлементный экземпляр `TelemetryClient` уже зарегистрирован в контейнер с внедрением Зависимостей, которая использует `TelemetryConfiguration` с остальной частью данных телеметрии. Создание нового `TelemetryClient` экземпляра рекомендуется только в том случае, если он должен иметь отдельные конфигурации от остальной части данных телеметрии. В следующем примере показано, как отслеживать дополнительные данные телеметрии из контроллера.

```csharp
using Microsoft.ApplicationInsights;

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

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Некоторые шаблоны Visual Studio используется метод расширения UseApplicationInsights() на IWebHostBuilder для включения Application Insights. Такое использование по-прежнему действительна?

Включение Application Insights с помощью этого метода является допустимым и используется в Visual Studio адаптации, а также в расширениях веб-приложения Azure также. Тем не менее, рекомендуется использовать `services.AddApplicationInsightsTelemetry()` так как он обеспечивает перегрузки для управления определенной настройки. Оба методы внутренне сделать то же самое, таким образом, если нет пользовательские конфигурации для применения, вызов либо подходит.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Я выполняю развертывание Мое приложение ASP.NET Core, веб-приложения Azure. Следует по-прежнему включать расширение Application Insights из веб-приложений?

Если пакет SDK установлен во время сборки, как показано в этой статье, нет необходимости включить расширение Application Insights на портале службы приложений. Даже если расширение установлено, оно будет пассивный режим когда обнаруживает, что пакет SDK уже добавлен в приложение. Включение Application Insights из расширения освобождает вас от установки и обновления пакета SDK. Тем не менее Включение Application Insights в соответствии с этой статьи является более гибким по причинам ниже.
   * Телеметрия Application Insights будут продолжать работать:
       * Все операционные системы — Windows, Linux, Mac.
       * Все публикации режимов — автономные или зависящего от платформы.
       * Часы всех целевых платформ, включая полную версию .NET Framework.
       * Все параметры размещения - веб-приложения Azure, виртуальные машины, Linux, контейнеры, AKS, не относящиеся к Azure.
   * Данные телеметрии можно увидеть локально, при отладке из Visual Studio.
   * Позволяет использовать дополнительные пользовательские телеметрические данные отслеживания `TrackXXX()` API.
   * У вас есть полный контроль над конфигурацией.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Включить мониторинг Application Insights с помощью таких средств, как монитор состояния?

№ [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и их замене предстоящих [v2 монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) в настоящее время поддерживает ASP.NET 4.x только.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>У меня есть приложение ASP.NET Core 2.0. Отключена ли Application Insights автоматически без мне делать?

`Microsoft.AspNetCore.All` метапакет 2.0 включены в пакет SDK Application Insights (версия 2.1.0), и при запуске приложения в отладчике Visual Studio, Visual Studio позволяет Application Insights и отображаются данные телеметрии, локально в самой интегрированной среды разработки. Данные телеметрии не было отправлено в службу Application Insights, если не задан явно ключ инструментирования. Мы рекомендуем следовать инструкциям в этой статье, как включить Application Insights, даже для 2.0 приложения.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Запустим Мое приложение в Linux. Все функции в Linux также поддерживаются?

* Да. Поддержка функций пакета SDK для совпадает на всех платформах, за исключением следующих случаев:

    * Счетчики производительности еще не поддерживаются в не-Windows.
    * Несмотря на то что `ServerTelemetryChannel` включена по умолчанию, если приложение выполняется в Linux или MacOS, канал не создает автоматически папку локального хранилища для хранения данных телеметрии временно, при наличии проблемы с сетью. Это ограничение приводит к телеметрии будут потеряны, если временные сети или проблемы сервера. Возможным решением этой проблемы является для пользователя настроить локальную папку для канала, как показано ниже.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

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

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Видео

- Пошаговые видеоинструкции по [настройке Application Insights с помощью .NET Core и Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Внешнее пошаговые видео о [Настройка Application Insights с использованием .NET Core и Visual Studio Code](https://youtu.be/ygGt84GDync) с нуля.

## <a name="next-steps"></a>Дальнейшие действия
* [Изучите маршруты пользователей](../../azure-monitor/app/usage-flows.md), чтобы понять, как пользователи перемещаются через приложение.
* [Настройте коллекцию моментальных снимков](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) для отображения состояния исходного кода и переменных в момент создания исключения.
* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
* Используйте [тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md), чтобы постоянно проверять работу приложения из всех точек мира.
