---
title: Application Insights Azure для ASP.NET Core приложений | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений ASP.NET Core.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/22/2019
ms.openlocfilehash: 5b9b92cd39e8d540f784d82d6c7f4a5754c85b62
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677730"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights для ASP.NET Core приложений

В этой статье описано, как включить Application Insights для [ASP.NET Core](https://docs.microsoft.com/aspnet/core) приложения. После выполнения инструкций, описанных в этой статье, Application Insights соберет запросы, зависимости, исключения, счетчики производительности, пульсы и журналы из приложения ASP.NET Core.

В качестве примера мы будем использовать [приложение MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) , предназначенное для `netcoreapp2.2`. Эти инструкции можно применить ко всем ASP.NET Coreным приложениям.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

[Пакет SDK Application Insights для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) может отслеживать приложения независимо от того, где или как они выполняются. Если приложение работает и имеет сетевое подключение к Azure, можно собирать данные телеметрии. Мониторинг Application Insights поддерживается везде, где поддерживается .NET Core. Поддержка:
* **Операционная система**: Windows, Linux или Mac.
* **Метод размещения**: в процессе или вне процесса.
* **Метод развертывания**: зависимая от платформы или автономная.
* **Веб-сервер**: IIS (Internet Information Server) или Kestrel.
* **Платформа размещения**: функции веб-приложений службы приложений Azure, виртуальной машины Azure, Docker, службы Azure KUBERNETES (AKS) и т. д.
* **Версия среды выполнения .NET Core**: 1. XX, 2. XX или 3. XX
* **Интегрированная среда разработки**: Visual Studio, VS Code или Командная строка.

> [!NOTE]
> Если вы используете ASP.NET Core 3,0 вместе с Application Insights, используйте версию [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) или более позднюю. Это единственная версия, которая поддерживает ASP.NET Core 3,0.

## <a name="prerequisites"></a>Технические условия

- Работающее приложение ASP.NET Core. Если необходимо создать ASP.NET Core приложение, следуйте указаниям в этом [ASP.NET Coreном руководстве](https://docs.microsoft.com/aspnet/core/getting-started/).
- Допустимый ключ инструментирования Application Insights. Этот ключ необходим для отправки любых данных телеметрии в Application Insights. Если необходимо создать новый Application Insights ресурс для получения ключа инструментирования, см. раздел [Создание ресурса Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Включение телеметрии на стороне сервера Application Insights (Visual Studio)

1. Откройте проект в Visual Studio.

    > [!TIP]
    > При необходимости можно настроить систему управления версиями для проекта, чтобы можно было отвестися от всех изменений, которые Application Insights делает. Чтобы включить систему управления версиями, выберите **файл**  > **Добавить в систему управления версиями**.

2. Выберите **Проект** > **Добавить телеметрию Application Insights**.

3. Выберите **Начать**. Текст этого варианта может отличаться в зависимости от используемой версии Visual Studio. В некоторых более ранних версиях вместо нее используется кнопка **запустить бесплатно** .

4. Выберите свою подписку. Затем выберите **ресурс**  > **регистр**.

5. После добавления Application Insights в проект убедитесь, что вы используете последний стабильный выпуск пакета SDK. Перейдите в **проект**  > **Управление пакетами NuGet**  > **Microsoft. ApplicationInsights. AspNetCore**. При необходимости выберите **Обновить**.

     ![Снимок экрана, на котором показано, где выбрать пакет Application Insights для обновления](./media/asp-net-core/update-nuget-package.png)

6. Если вы**применяете**необязательную подсказку и добавили проект в систему управления версиями, перейдите в раздел **View**  > **Team Explorer**  >  Changes. Затем выберите каждый файл, чтобы просмотреть различие изменений, внесенных Application Insights телеметрии.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Включить телеметрию на стороне сервера Application Insights (без Visual Studio)

1. Установите [пакет NuGet для Application Insights SDK для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Рекомендуется всегда использовать последнюю стабильную версию. Ознакомьтесь с полными сведениями о выпуске пакета SDK в [репозитории GitHub с открытым исходным кодом](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    В следующем примере кода показаны изменения, добавляемые в файл `.csproj` проекта.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.0" />
        </ItemGroup>
    ```

2. Добавьте `services.AddApplicationInsightsTelemetry();` в метод `ConfigureServices()` в классе `Startup`, как показано в следующем примере:

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

    Хотя ключ инструментирования можно указать в качестве аргумента для `AddApplicationInsightsTelemetry`, рекомендуется указать ключ инструментирования в конфигурации. В следующем примере кода показано, как указать ключ инструментирования в `appsettings.json`. Убедитесь, что `appsettings.json` копируется в корневую папку приложения во время публикации.

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

    Кроме того, можно указать ключ инструментирования в любой из следующих переменных среды:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Пример.

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Как правило, `APPINSIGHTS_INSTRUMENTATIONKEY` указывает ключ инструментирования для приложений, развернутых в веб-приложениях Azure.

    > [!NOTE]
    > Ключ инструментирования, указанный в коде, передается по переменной среды `APPINSIGHTS_INSTRUMENTATIONKEY`, которая WINS поверх других параметров.

## <a name="run-your-application"></a>Запуск приложения

Запустите приложение и выполните запросы к нему. Теперь данные телеметрии должны передаваться в Application Insights. Пакет SDK для Application Insights автоматически собирает входящие веб-запросы в приложение, а также следующие данные телеметрии.

### <a name="live-metrics"></a>Динамические метрики

Для быстрой проверки правильности настройки Application Insights мониторинга можно использовать [динамические метрики](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) . Это может занять несколько минут, прежде чем данные телеметрии будут отображаться на портале и в аналитике, в динамических метриках будет показано использование ЦП запущенного процесса практически в реальном времени. Он также может отображать другие данные телеметрии, такие как запросы, зависимости, трассировки и т. д.

### <a name="ilogger-logs"></a>Журналы ILogger

Журналы, созданные с помощью `ILogger` серьезности `Warning` или более, автоматически фиксируются. Следуйте инструкциям [ILogger](ilogger.md#control-logging-level) , чтобы настроить, какие уровни журнала фиксируются Application Insights.

### <a name="dependencies"></a>Зависимости

Коллекция зависимостей включена по умолчанию. В [этой](asp-net-dependencies.md#automatically-tracked-dependencies) статье объясняются автоматически собираемые зависимости, а также инструкции по отслеживанию вручную.

### <a name="performance-counters"></a>Счетчики производительности

Поддержка [счетчиков производительности](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) в ASP.NET Core ограничена:

* Версии пакета SDK 2.4.1 и более поздних версий собираются счетчики производительности, если приложение выполняется в веб-приложениях Azure (Windows).
* Версии пакета SDK 2.7.1 и более поздних версий собираются счетчики производительности, если приложение выполняется в Windows и нацелены `NETSTANDARD2.0` или более поздней версии.
* Для приложений, предназначенных для .NET Framework, все версии пакетов SDK поддерживают счетчики производительности.
* Версии пакета SDK 2.8.0 и более поздней поддерживают счетчик ЦП/памяти в Linux. В Linux не поддерживается никакой другой счетчик. Рекомендуемый способ получения системных счетчиков в Linux (и других средах, отличных от Windows) — с помощью [евенткаунтерс](#eventcounter)

### <a name="eventcounter"></a>евенткаунтер

`EventCounterCollectionModule` включен по умолчанию и будет собираются набор счетчиков по умолчанию из приложений .NET Core 3,0. В руководстве по [евенткаунтер](eventcounters.md) представлен набор собираемых счетчиков. Он также содержит инструкции по настройке списка.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Включение телеметрии на стороне клиента для веб-приложений

Предыдущие шаги достаточно, чтобы помочь начать сбор данных телеметрии на стороне сервера. Если приложение имеет клиентские компоненты, выполните следующие действия, чтобы начать сбор данных [телеметрии использования](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. В `_ViewImports.cshtml` добавьте внедрение:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. В `_Layout.cshtml` вставьте `HtmlHelper` в конце раздела `<head>`, но перед любым другим сценарием. Если вы хотите сообщить о любых пользовательских данных телеметрии JavaScript со страницы, вставьте ее после этого фрагмента кода:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Ранее указанные имена файлов `.cshtml` относятся к шаблону приложения MVC по умолчанию. В конечном счете, если вы хотите правильно включить наблюдение на стороне клиента для вашего приложения, фрагмент JavaScript должен отображаться в разделе `<head>` каждой страницы приложения, которое требуется отслеживать. Эту цель можно выполнить для этого шаблона приложения, добавив фрагмент кода JavaScript в `_Layout.cshtml`. 

Если проект не содержит `_Layout.cshtml`, вы по-прежнему можете добавить [наблюдение на стороне клиента](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Это можно сделать, добавив фрагмент кода JavaScript в эквивалентный файл, который управляет `<head>` всех страниц в приложении. Кроме того, можно добавить фрагмент кода на несколько страниц, но это решение сложно поддерживать, и в большинстве случаев это не рекомендуется.

## <a name="configure-the-application-insights-sdk"></a>Настройка пакета SDK для Application Insights

Можно настроить Application Insights SDK для ASP.NET Core, чтобы изменить конфигурацию по умолчанию. Пользователи Application Insights ASP.NET SDK могут быть знакомы с изменением конфигурации с помощью `ApplicationInsights.config` или путем изменения `TelemetryConfiguration.Active`. Конфигурация изменяется иначе для ASP.NET Core. Добавьте пакет SDK для ASP.NET Core в приложение и настройте его с помощью ASP.NET Core встроенного [внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Сделайте почти все изменения конфигурации в методе `ConfigureServices()` класса `Startup.cs`, если не указано иное. Дополнительные сведения см. в следующих разделах.

> [!NOTE]
> В ASP.NET Coreных приложениях изменение конфигурации путем изменения `TelemetryConfiguration.Active` не поддерживается.

### <a name="using-applicationinsightsserviceoptions"></a>Использование Аппликатионинсигхтссервицеоптионс

Можно изменить несколько общих параметров, передав `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`, как показано в следующем примере:

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

Полный список параметров в `ApplicationInsightsServiceOptions`

|Параметр | Описание | значение по умолчанию
|---------------|-------|-------
|енаблекуиккпулсеметрикстреам | Включить или отключить функцию Ливеметрикс | true
|енаблеадаптивесамплинг | Включение или отключение адаптивной выборки | true
|енаблехеартбеат | Функция "включить/отключить пульс", которая периодически (по умолчанию составляет 15 минут) отправляет пользовательскую метрику "Хеартбеатстате" со сведениями о среде выполнения, такими как версия .NET, сведения о среде Azure, если применимо, и т. д. | true
|аддаутоколлектедметрицекстрактор | Включите или отключите средство извлечения Аутоколлектедметрикс, которое представляет собой Телеметрипроцессор, который отправляет предварительно агрегированные метрики о запросах и зависимостях перед выполнением выборки. | true
|Рекуестколлектионоптионс. Траккексцептионс | Включение и отключение отчетов о необработанном отслеживании исключений модулем сбора запросов. | false в NETSTANDARD 2.0 (поскольку исключения отправляются с помощью Аппликатионинсигхтслогжерпровидер), в противном случае — значение true.

Просмотрите [Настраиваемые параметры в `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) для наиболее актуального списка.

### <a name="sampling"></a>Выборка

Пакет SDK Application Insights для ASP.NET Core поддерживает как фиксированную, так и адаптивную выборку. Адаптивная выборка включена по умолчанию. 

Дополнительные сведения см. в статье [Настройка адаптивной выборки для приложений ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Добавление Telemetryinitializer

Используйте [инициализаторы телеметрии](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) , если необходимо определить глобальные свойства, которые отправляются со всеми данными телеметрии.

Добавьте новые `TelemetryInitializer` в контейнер `DependencyInjection`, как показано в следующем коде. Пакет SDK автоматически берет все `TelemetryInitializer`, добавленные в контейнер `DependencyInjection`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Удаление Telemetryinitializer

Инициализаторы телеметрии представлены по умолчанию. Чтобы удалить все или конкретные инициализаторы телеметрии, используйте следующий пример кода *после* вызова `AddApplicationInsightsTelemetry()`.

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

Вы можете добавить пользовательские обработчики данных телеметрии для `TelemetryConfiguration` с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` на `IServiceCollection`. В [сценариях расширенной фильтрации](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)используются обработчики данных телеметрии. Используйте следующий пример.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Настройка или удаление Телеметримодулес по умолчанию

Application Insights использует модули телеметрии для автоматического сбора полезных данных телеметрии о конкретных рабочих нагрузках, не требуя ручного отслеживания пользователем.

Следующие модули автоматической коллекции включены по умолчанию. Эти модули отвечают за автоматическое сбор данных телеметрии. Их можно отключить или настроить для изменения их поведения по умолчанию.

* `RequestTrackingTelemetryModule` — собирает RequestTelemetry из входящих веб-запросов.
* `DependencyTrackingTelemetryModule` — собирает DependencyTelemetry из исходящих вызовов HTTP и вызовов SQL.
* `PerformanceCollectorModule` — собирает данные счетчиков производительности Windows.
* `QuickPulseTelemetryModule` — собирает данные телеметрии для отображения на портале динамических метрик.
* `AppServicesHeartbeatTelemetryModule` — собирает сведения о неходе сердца (которые отправляются в виде пользовательских метрик), о среде службы приложений Azure, в которой размещено приложение.
* `AzureInstanceMetadataTelemetryModule` — собирает сведения о неходе сердца (которые отправляются в виде пользовательских метрик), о среде виртуальной машины Azure, в которой размещено приложение.
* `EventCounterCollectionModule` — собирает [евенткаунтерс.](eventcounters.md) Этот модуль является новой функцией и доступен в пакете SDK версии 2.8.0 и выше.

Чтобы настроить любое `TelemetryModule` по умолчанию, используйте метод расширения `ConfigureTelemetryModule<T>` в `IServiceCollection`, как показано в следующем примере.

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

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

### <a name="configuring-a-telemetry-channel"></a>Настройка канала телеметрии

Канал по умолчанию — `ServerTelemetryChannel`. Его можно переопределить, как показано в следующем примере.

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

### <a name="disable-telemetry-dynamically"></a>Динамическое отключение телеметрии

Если вы хотите отключить данные телеметрии условно и динамически, вы можете разрешить `TelemetryConfiguration` экземпляр с контейнером внедрения зависимостей ASP.NET Core в любом месте кода и установить для него флажок `DisableTelemetry`.

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

Указанные выше данные не предотвращают сбор данных телеметрии для модулей автоматического сбора данных. С помощью описанного выше подхода отключается только отправка данных телеметрии в Application Insights. Если не требуется определенный модуль автоматической коллекции, рекомендуется [удалить модуль телеметрии](#configuring-or-removing-default-telemetrymodules) .

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-application-insights-support-aspnet-core-30"></a>Поддерживает ли Application Insights ASP.NET Core 3,0?

Да. Обновите [пакет SDK для Application Insights для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.8.0 или более поздней. Более старые версии пакета SDK не поддерживают ASP.NET Core 3,0.

Кроме того, если вы используете инструкции на основе Visual Studio [отсюда,](#enable-application-insights-server-side-telemetry-visual-studio)обновите ее до последней версии Visual Studio 2019 (16.3.0). Предыдущие версии Visual Studio не поддерживают автоматическую адаптации для приложений ASP.NET Core 3,0.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Как можно отвестить данные телеметрии, которые не собираются автоматически?

Получите экземпляр `TelemetryClient` с помощью внедрения конструктора и вызовите для него необходимый метод `TrackXXX()`. Не рекомендуется создавать новые экземпляры `TelemetryClient` в приложении ASP.NET Core. Одноэлементный экземпляр `TelemetryClient` уже зарегистрирован в контейнере `DependencyInjection`, который совместно использует `TelemetryConfiguration` с остальными данными телеметрии. Создание нового экземпляра `TelemetryClient` рекомендуется только в том случае, если требуется конфигурация, отделяющая остальные данные телеметрии.

В следующем примере показано, как отслеживанию дополнительных данных телеметрии из контроллера.

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

Дополнительные сведения о настраиваемых отчетах данных в Application Insights см. в разделе [Справочник по API настраиваемых метрик Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Некоторые шаблоны Visual Studio использовали метод расширения UseApplicationInsights () в Ивебхостбуилдер для включения Application Insights. Является ли это использование допустимым?

Хотя метод расширения `UseApplicationInsights()` по-прежнему поддерживается, он отмечается как устаревший в пакете SDK для Application Insights версии 2.8.0. Он будет удален в следующей основной версии пакета SDK. Для включения телеметрии Application Insights рекомендуется использовать `AddApplicationInsightsTelemetry()`, так как он обеспечивает перегрузки для управления некоторой конфигурацией. Кроме того, в ASP.NET Core приложениях 3,0 `services.AddApplicationInsightsTelemetry()` является единственным способом включения Application Insights.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Я развертываю приложение ASP.NET Core в веб-приложениях. Следует ли по-прежнему включать расширение Application Insights из веб-приложений?

Если пакет SDK устанавливается во время сборки, как показано в этой статье, вам не нужно включать [расширение Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) на портале службы приложений. Даже если расширение установлено, оно будет восстановлено при обнаружении того, что пакет SDK уже добавлен в приложение. Если включить Application Insights из расширения, вам не нужно устанавливать и обновлять пакет SDK. Но если вы включили Application Insights, следуя инструкциям в этой статье, вы получите большую гибкость, поскольку:

   * Данные телеметрии Application Insights будут продолжать работать в:
       * Все операционные системы, включая Windows, Linux и Mac.
       * Все режимы публикации, включая автономные или зависящие от платформы.
       * Все целевые платформы, включая полный .NET Framework.
       * Все варианты размещения, включая веб-приложения, виртуальные машины, Linux, контейнеры, службу Azure Kubernetes и размещение без Azure.
       * Все версии .NET Core, включая предварительные версии.
   * Вы можете просматривать данные телеметрии локально при отладке из Visual Studio.
   * Дополнительные пользовательские данные телеметрии можно отвести с помощью API `TrackXXX()`.
   * Вы полностью контролируете конфигурацию.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Можно ли включить Application Insights мониторинг с помощью таких средств, как монитор состояния?

Нет. В настоящее время [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и [Монитор состояния v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) поддерживают только ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Включено ли Application Insights для приложения ASP.NET Core 2,0 автоматически?

В `Microsoft.AspNetCore.All` 2,0 метапакет входит пакет SDK для Application Insights (версия 2.1.0). При запуске приложения в отладчике Visual Studio Visual Studio включает Application Insights и отображает данные телеметрии локально в самой интегрированной среде разработки. Данные телеметрии не были отправлены в службу Application Insights, если не был указан ключ инструментирования. Мы рекомендуем использовать инструкции, приведенные в этой статье, чтобы включить Application Insights, даже для приложений 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Если я запускаю приложение в Linux, поддерживаются ли все функции?

Да. Поддержка функций для пакета SDK одинакова на всех платформах, за исключением следующих:

* Счетчики производительности поддерживаются только в Windows.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-30-worker-service-template-applications"></a>Поддерживается ли этот пакет SDK для новых приложений-шаблонов рабочих служб .NET Core 3,0?

Этот пакет SDK требует `HttpContext` и, следовательно, не работает в приложениях, отличных от HTTP, включая приложения рабочей службы .NET Core 3,0. Ознакомьтесь с [этим](worker-service.md) документом, чтобы включить Application Insights в таких приложениях с помощью ВЫПУЩЕННОГО пакета SDK для Microsoft. ApplicationInsights. воркерсервице.

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Видео

- Ознакомьтесь с этим внешним пошаговым видео, чтобы [настроить Application Insights с .NET Core и Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) с нуля.
- Ознакомьтесь с этим внешним пошаговым видео, чтобы [настроить Application Insights с .NET Core и Visual Studio Code](https://youtu.be/ygGt84GDync) с нуля.

## <a name="next-steps"></a>Дальнейшие действия

* [Изучите потоки пользователей](../../azure-monitor/app/usage-flows.md) , чтобы понять, как пользователи переходят через приложение.
* [Настройте сбор моментальных снимков](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) для просмотра состояния исходного кода и переменных в момент возникновения исключения.
* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для получения подробного представления о производительности и использовании приложения.
* Используйте [тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md), чтобы постоянно проверять работу приложения из всех точек мира.
* [Внедрение зависимостей в ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
