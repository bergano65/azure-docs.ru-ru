---
title: Исследования приложений Azure для ASP.NET основных приложений Документы Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений ASP.NET Core.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 1a9a81d76df7f14fb99b8521e7bfa2edff6c9e9e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687384"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Анализ приложений для ASP.NET основных приложений

В этой статье описывается, как включить Application Insights для [ASP.NET приложения Core.](https://docs.microsoft.com/aspnet/core) Когда вы закончите инструкции в этой статье, Application Insights будет собирать запросы, зависимости, исключения, счетчики производительности, биения сердца и журналы из вашего приложения ASP.NET Core.

Примером, который мы будем использовать здесь, `netcoreapp2.2`является [приложение MVC,](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) которое нацелено на. Вы можете применить эти инструкции ко всем ASP.NET приложениям Core.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии

[Приложение Insights SDK для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) может контролировать ваши приложения независимо от того, где и как они работают. Если приложение работает и имеет подключение к Azure, можно собрать телеметрию. Мониторинг Application Insights поддерживается везде .NET Core. Обложки поддержки:
* **Операционная система**: Windows, Linux или Mac.
* **Метод хостинга**: В процессе или вне процесса.
* **Метод развертывания**: Инфраструктура зависима или автономна.
* **Веб-сервер**: IIS (Интернет информационный сервер) или Kestrel.
* **Платформа хостинга**: Функция Web Apps службы приложений Azure, Azure VM, Docker, Azure Kubernetes Service (AKS) и так далее.
* **.NET Core Runtime версия**: 1.XX, 2.XX, или 3.XX
* **IDE**: Визуальная студия, VS Code или командная строка.

> [!NOTE]
> Если вы используете ASP.NET Core 3.X вместе с Application Insights, пожалуйста, используйте версию [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) или выше. Это единственная версия, которая поддерживает ASP.NET Core 3.X.

## <a name="prerequisites"></a>Предварительные требования

- Функционирующее приложение ASP.NET Core. Если вам нужно создать приложение ASP.NET Core, следуйте этому [ASP.NET учебнику Core.](https://docs.microsoft.com/aspnet/core/getting-started/)
- Действительный ключ для анализа приложений. Этот ключ необходим для отправки любой телеметрии в Application Insights. Если вам нужно создать новый ресурс Application Insights для получения ключа приборов, [см.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Включить телеметрию сервера На стороне приложения (Visual Studio)

1. Откройте проект в Visual Studio.

    > [!TIP]
    > При желании можно настроить исходное управление для проекта, чтобы отслеживать все изменения, внесенные ВApplication. Для обеспечения управления исходом выберите **файл** > **Добавить в элемент управления исходом.**

2. Выберите **проект** > **Добавить приложение Исследования Телеметрия**.

3. Выберите **Начало работы**. Текст этого выбора может варьироваться в зависимости от вашей версии Visual Studio. Некоторые более ранние версии вместо этого используют кнопку **Start Free.**

4. Выберите свою подписку. Затем выберите**Регистр** **ресурсов** > .

5. После добавления приложения Insights в проект проверьте, что вы используете последний стабильный выпуск SDK. Перейти к **проекту** > **Управления NuGet пакеты** > **Microsoft.ApplicationInsights.AspNetCore**. Если вам нужно, выберите **обновление**.

     ![Скриншот, показывающий, где выбрать пакет Application Insights для обновления](./media/asp-net-core/update-nuget-package.png)

6. Если вы следовали необязательным чаевым и добавили проект в исходный элемент управления, перейдите к **View** > **Team Explorer** > **Changes.** Затем выберите каждый файл, чтобы увидеть дифф-представление об изменениях, внесенных телеметрией Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Включить приложение Исследования сервера стороне телеметрии (без визуальной студии)

1. Установите [пакет Application Insights SDK NuGet для ASP.NET Core.](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Мы рекомендуем вам всегда использовать последнюю стабильную версию. Найти полные заметки релиз для SDK на [с открытым исходным кодом GitHub репо](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Следующий пример кода показывает изменения, которые должны `.csproj` быть добавлены в файл проекта.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Добавьте `services.AddApplicationInsightsTelemetry();` `ConfigureServices()` к методу в классе, `Startup` как в этом примере:

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

3. Настройка ключа приборов.

    Хотя вы можете предоставить ключ приборов в качестве `AddApplicationInsightsTelemetry`аргумента, мы рекомендуем вам указать ключ приборов в конфигурации. Следующий пример кода показывает, как указать ключ приборов в `appsettings.json`. Убедитесь, что `appsettings.json` во время публикации его скопировали в корневую папку приложения.

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

    Кроме того, укажите ключ приборов в любой из следующих переменных среды:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Пример:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Как `APPINSIGHTS_INSTRUMENTATIONKEY` правило, определяется ключ приборов для приложений, развернутых в Web Apps Azure.

    > [!NOTE]
    > Ключ приборов, указанный в коде, побеждает переменную `APPINSIGHTS_INSTRUMENTATIONKEY`среды, которая побеждает другие опции.

## <a name="run-your-application"></a>Запуск приложения

Запустите приложение и сделайте запросы на него. Телеметрия теперь должна поступать в Application Insights. Приложение Insights SDK автоматически собирает входящие веб-запросы в ваше приложение, а также следующую телеметрию.

### <a name="live-metrics"></a>Динамические метрики

[Метрики в реальном времени](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) могут быть использованы для быстрого проверки правильного мониторинга Application Insights. Хотя это может занять несколько минут, прежде чем телеметрия начинает появляться в портале и аналитики, Live Metrics покажет использование процессора процесса работы в почти в режиме реального времени. Он также может отображать другие телеметрии, такие как Запросы, Зависимости, Следы и т.д.

### <a name="ilogger-logs"></a>Логи ILogger

Входы, излучаемые с помощью `ILogger` тяжести `Warning` или больше, автоматически фиксируются. Следуйте [iLogger документы,](ilogger.md#control-logging-level) чтобы настроить, какие уровни журнала захвачены Application Insights.

### <a name="dependencies"></a>Зависимости

Коллекция зависимостей включена по умолчанию. [В этой](asp-net-dependencies.md#automatically-tracked-dependencies) статье разъясняются автоматически собранные зависимости, а также содержатся шаги для ручного отслеживания.

### <a name="performance-counters"></a>Счетчики производительности

Поддержка [счетчиков производительности](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) в ASP.NET Core ограничена:

* SDK версии 2.4.1, а затем собирать счетчики производительности, если приложение работает в Azure Web Apps (Windows).
* SDK версии 2.7.1, а затем собирать счетчики производительности, если приложение работает в Windows и цели `NETSTANDARD2.0` или позже.
* Для приложений, ориентированных на рамочную систему .NET, все версии счетчиков производительности SDK поддерживаются.
* SDK Версии 2.8.0 и позже поддержка cpu / счетчик памяти в Linux. Ни один другой счетчик не поддерживается в Linux. Рекомендуемый способ получения счетчиков системы в Linux (и других средах, не связанных с Windows), заключается в использовании [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`включен по умолчанию, и он будет собирать набор счетчиков по умолчанию из приложений .NET Core 3.X. В учебнике [EventCounter](eventcounters.md) перечислен набор собранных счетчиков по умолчанию. Он также имеет инструкции по настройке списка.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Включить телеметрию на стороне клиента для веб-приложений

Предшествующих шагов достаточно, чтобы помочь вам начать сбор телеметрии на стороне сервера. Если в приложении есть компоненты со стороны клиента, выполните следующие шаги, чтобы начать сбор [телеметрии использования.](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

1. В `_ViewImports.cshtml`, добавить инъекции:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. В `_Layout.cshtml`, `HtmlHelper` вставить в `<head>` конце раздела, но до любого другого сценария. Если вы хотите сообщить о любой пользовательской телеметрии JavaScript со страницы, введите ее после этого фрагмента:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Альтернативно к `FullScript` использованию `ScriptBody` доступен начиная с SDK v2.14. Используйте это, если `<script>` вам нужно управлять тегом, чтобы установить политику безопасности содержимого:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Имена `.cshtml` файлов, на которые упоминались ранее, взяты из шаблона приложения MVC по умолчанию. В конечном счете, если вы хотите должным образом включить мониторинг клиентской стороны для вашего приложения, фрагмент JavaScript должен отображаться в `<head>` разделе каждой страницы приложения, который вы хотите контролировать. Вы можете достичь этой цели для этого шаблона приложения, добавив фрагмент JavaScript. `_Layout.cshtml` 

Если ваш проект не `_Layout.cshtml`включает в себя, вы все равно можете добавить [мониторинг на стороне клиента.](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) Это можно сделать, добавив фрагмент JavaScript в эквивалентный `<head>` файл, который управляет всеми страницами в приложении. Или вы можете добавить фрагмент на несколько страниц, но это решение трудно поддерживать, и мы обычно не рекомендуем его.

## <a name="configure-the-application-insights-sdk"></a>Настройка приложения Исследования SDK

Можно настроить SDK Application Insights для ASP.NET Core для изменения конфигурации по умолчанию. Пользователи Application Insights ASP.NET SDK могут быть `ApplicationInsights.config` знакомы с `TelemetryConfiguration.Active`изменением конфигурации с помощью или путем изменения. Для ASP.NET Core конфигурацию по-разному меняют. Добавьте ASP.NET Core SDK в приложение и настройте его с помощью [встроенной инъекции ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)Core. Внося почти все `ConfigureServices()` изменения `Startup.cs` конфигурации в метод вашего класса, если вы не направлены в противном случае. В следующих разделах предлагается более подробная информация.

> [!NOTE]
> В ASP.NET основных приложений `TelemetryConfiguration.Active` изменение конфигурации путем изменения не поддерживается.

### <a name="using-applicationinsightsserviceoptions"></a>Использование ApplicationApplicationInsightsServiceOptions

Вы можете изменить несколько общих параметров, перейдя `ApplicationInsightsServiceOptions` к, `AddApplicationInsightsTelemetry`как в этом примере:

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

Полный список настроек в`ApplicationInsightsServiceOptions`

|Параметр | Описание | Значение по умолчанию
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Включить/отключить`PerformanceCounterCollectionModule` | Да
|ВключитьЗапросОтслеживаниеТелеметрииМодуль   | Включить/отключить`RequestTrackingTelemetryModule` | Да
|EnableEventCounterCollectionModule   | Включить/отключить`EventCounterCollectionModule` | Да
|EnableDependencyTrackingTelemetryModule   | Включить/отключить`DependencyTrackingTelemetryModule` | Да
|EnableAppServicesHeartbeatTelemetryModule  |  Включить/отключить`AppServicesHeartbeatTelemetryModule` | Да
|EnableAzureInstanceMetadataТелеметрияМодуль   |  Включить/отключить`AzureInstanceMetadataTelemetryModule` | Да
|ВключитьКвипулпульсМетрический поток | Функция включить/отключить LiveMetrics | Да
|ВключитьАдаптивнуювыборную выборку | Включить/отключить адаптивную выборку | Да
|ВключитьСердцебить | Функция включить/отключить сердцебиение, которая периодически (15-минутный по умолчанию) отправляет пользовательский метрику под названием "HeartBeatState" с информацией о времени выполнения, как .NET Version, информация об окружающей среде Azure, если это применимо, и т.д. | Да
|AddAutoCollectedМетрический экстрактор | Включить / отключить экстрактор AutoCollectedMetrics, который является TelemetryProcessor, который посылает предварительно агрегированные метрики о запросах / зависимостей до выборки происходит. | Да
|RequestCollectionOptions.TrackИсключения | Включить/отключить отчетность о необработанном отслеживании исключений модулем сбора запросов. | ложные в NETSTANDARD2.0 (потому что исключения отслеживаются с ApplicationInsightsLoggerProvider), правда в противном случае.

Ознакомьтесь с [настраиваемыми настройками для `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) самого актуального списка.

### <a name="sampling"></a>Выборка

Приложение Insights SDK для ASP.NET Core поддерживает как фиксированную, так и адаптивную выборку. Адаптивная выборка включена по умолчанию. 

Для получения дополнительной информации см [ASP.NET.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

### <a name="adding-telemetryinitializers"></a>Добавление телеметрии Первоначальные

Используйте [инициализаторы телеметрии,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) когда требуется определить глобальные свойства, которые отправляются со всей телеметрией.

Добавьте `TelemetryInitializer` в `DependencyInjection` контейнер все новое, как показано в следующем коде. SDK автоматически забирает `TelemetryInitializer` все добавленные `DependencyInjection` в контейнер.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Удаление телеметрии Первоначальные

Инициали телеметрии присутствуют по умолчанию. Чтобы удалить все или конкретные инициали телеметрии, используйте следующий код образца *после* вызова. `AddApplicationInsightsTelemetry()`

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

### <a name="adding-telemetry-processors"></a>Добавление телеметрических процессоров

Вы можете добавить пользовательские `TelemetryConfiguration` телеметрические `AddApplicationInsightsTelemetryProcessor` процессоры с помощью метода расширения на `IServiceCollection`. Вы используете телеметрические процессоры в [расширенных сценариях фильтрации.](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) Используйте следующий пример.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Настройка или удаление телеметрических модулей по умолчанию

Application Insights использует модули телеметрии для автоматического сбора полезной телеметрии о конкретных рабочих нагрузках, не требуя ручного отслеживания пользователем.

Следующие модули автоматического сбора включены по умолчанию. Эти модули отвечают за автоматический сбор телеметрии. Вы можете отключить или настроить их, чтобы изменить их поведение по умолчанию.

* `RequestTrackingTelemetryModule`- Собирает RequestTelemetry от входящих веб-запросов.
* `DependencyTrackingTelemetryModule`- Собирает зависимостьТелеметрия от исходящих http звонков и квл звонков.
* `PerformanceCollectorModule`- Собирает Windows PerformanceCounters.
* `QuickPulseTelemetryModule`- Собирает телеметрию для показа на портале Live Metrics.
* `AppServicesHeartbeatTelemetryModule`- Собирает биения сердца (которые отправляются в виде пользовательских метрик) о среде службы приложений Azure, где размещается приложение.
* `AzureInstanceMetadataTelemetryModule`- Собирает биения сердца (которые отправляются в виде пользовательских метрик) о среде Azure VM, где размещается приложение.
* `EventCounterCollectionModule`- Собирает [EventCounters.](eventcounters.md) Этот модуль является новой функцией и доступен в версии SDK 2.8.0 и выше.

Для настройки `TelemetryModule`любого по `ConfigureTelemetryModule<T>` умолчанию используйте метод расширения, `IServiceCollection`как показано в следующем примере.

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

Начиная с версии 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) содержит легкую опцию для отправления любого из модулей по умолчанию.

### <a name="configuring-a-telemetry-channel"></a>Настройка телеметрического канала

Канал по `ServerTelemetryChannel`умолчанию . Вы можете переопределить его, как показано в следующем примере.

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

### <a name="disable-telemetry-dynamically"></a>Отключить телеметрию динамически

Если вы хотите отключить телеметрию условно и динамично, вы можете разрешить `TelemetryConfiguration` экземпляр с ASP.NET `DisableTelemetry` контейнера для впрыска зависимости Core в любом месте вашего кода и установить флаг на нем.

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

Вышеупомянутое не мешает никаким модулям автоматического сбора собирать телеметрию. Только отправка телеметрии в Application Insights отключается при приведенном выше подходе. Если определенный модуль автоматического сбора не желателен, лучше удалить [модуль телеметрии](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-application-insights-support-aspnet-core-3x"></a>Поддерживает сяопролеты приложений ASP.NET Core 3.X?

Да. Обновление [для приложения Исследования SDK для ASP.NET основной](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.8.0 или выше. Старые версии SDK не поддерживают ASP.NET Core 3.X.

Кроме того, если вы используете Visual Studio на основе инструкций [отсюда](#enable-application-insights-server-side-telemetry-visual-studio), обновление до последней версии Visual Studio 2019 (16.3.0) на борту. Предыдущие версии Visual Studio не поддерживают автоматическое посадку для ASP.NET приложений Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Как отследить телеметрию, которая не собирается автоматически?

Получить экземпляр `TelemetryClient` с помощью впрыска `TrackXXX()` конструктора, и вызвать необходимый метод на нем. Мы не рекомендуем создавать `TelemetryClient` новые экземпляры в приложении ASP.NET Core. Однотонный `TelemetryClient` экземпляр уже зарегистрирован `DependencyInjection` в контейнере, который разделяет `TelemetryConfiguration` с остальной частью телеметрии. Создание нового `TelemetryClient` экземпляра рекомендуется только в том случае, если ему нужна конфигурация, отделенная от остальной телеметрии.

Ниже приводится следующий пример, как отслеживать дополнительную телеметрию от контроллера.

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

Для получения дополнительной информации о пользовательских данных отчетности в Application Insights, см [Application Insights пользовательские метрики API ссылки](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Некоторые шаблоны Visual Studio использовали метод расширения UseApplicationInsights () на IWebHostBuilder для включения приложений Insights. Является ли это использование по-прежнему действительны?

Хотя метод `UseApplicationInsights()` расширения все еще поддерживается, он помечен устаревшим в версии Application Insights SDK 2.8.0. Он будет удален в следующей основной версии SDK. Рекомендуемый способ включения телеметрии Application `AddApplicationInsightsTelemetry()` Insights — это использование, поскольку он обеспечивает перегрузки для управления некоторой конфигурацией. Кроме того, в ASP.NET приложений Core 3.X, `services.AddApplicationInsightsTelemetry()` это единственный способ включить понимание приложений.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Я развертываю свое приложение ASP.NET Core в Web-приложения. Должен ли я по-прежнему включать расширение Application Insights из веб-приложений?

Если SDK установлен во время сборки, как показано в этой статье, вам не нужно включать [расширение Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) с портала App Service. Даже если расширение установлено, оно откажется, когда обнаружит, что SDK уже добавлен в приложение. Если вы включите Application Insights из расширения, вам не нужно устанавливать и обновлять SDK. Но если вы включите Application Insights, следуя инструкциям в этой статье, у вас есть больше гибкости, потому что:

   * Телеметрия Application Insights продолжит работать в:
       * Все операционные системы, включая Windows, Linux и Mac.
       * Все способы публикации, включая автономные или зависимые рамки.
       * Все целевые рамки, включая полную рамку .NET.
       * Все варианты хостинга, включая web-приложения, ввосёр, Linux, контейнеры, сервис Azure Kubernetes и хостинг, не связанный с Azure.
       * Все версии .NET Core, включая предварительные версии.
   * Телеметрия можно увидеть локально, когда вы отлажаете от Visual Studio.
   * Вы можете отслеживать дополнительную пользовательскую `TrackXXX()` телеметрию с помощью API.
   * Вы имеете полный контроль над конфигурацией.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Могу ли я включить мониторинг applicationInsight с помощью таких инструментов, как Status Monitor?

Нет. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) и [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) в настоящее время поддерживают только ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Включается ли приложение Insights для моего приложения ASP.NET Core 2.0?

Метапакет `Microsoft.AspNetCore.All` 2.0 включал В приложении Insights SDK (версия 2.1.0). Если вы запустите приложение под Visual Studio, Visual Studio позволяет application Insights и показывает телеметрию локально в самой IDE. Телеметрия не была отправлена в службу Application Insights, если не был указан ключ прибора. Мы рекомендуем следовать инструкциям в этой статье, чтобы включить Application Insights, даже для 2.0 приложений.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Если я запускаю приложение в Linux, поддерживаются ли все функции?

Да. Поддержка функций для SDK одинакова на всех платформах, за следующими исключениями:

* SDK собирает [счетчики событий](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) на Linux, потому что [счетчики производительности](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) поддерживаются только в Windows. Большинство метрик одинаковы.
* Несмотря `ServerTelemetryChannel` на то, что приложение включено по умолчанию, если приложение работает в Linux или MacOS, канал автоматически не создает локальную папку хранения, чтобы временно сохранить телеметрию, если есть проблемы с сетью. Из-за этого ограничения телеметрия теряется при наличии временных проблем с сетью или сервером. Чтобы обойти эту проблему, назначайте локальную папку для канала:

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

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Поддерживается ли этот SDK для новых приложений для шаблонов службы поддержки работников .NET Core 3.X?

Этот SDK `HttpContext`требует, и, следовательно, не работает в любых не-HTTP приложений, в том числе .NET Core 3.X Приложения службы работников. Обратитесь к [этому](worker-service.md) документу для обеспечения информации о приложениях в таких приложениях, используя недавно выпущенный Microsoft.ApplicationInsights.WorkerService SDK.

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом

[Читайте и вносите свой вклад в код](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Видеоролик

- Проверьте это внешнее пошаговый видео для [настройки Application Insights с помощью .NET Core и Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) с нуля.
- Проверьте это внешнее пошаговое видео, чтобы [настроить Application Insights с помощью .NET Core и Visual Studio Code](https://youtu.be/ygGt84GDync) с нуля.

## <a name="next-steps"></a>Следующие шаги

* [Исследуйте потоки пользователей,](../../azure-monitor/app/usage-flows.md) чтобы понять, как пользователи перемещаются через ваше приложение.
* [Назначайте коллекцию моментальных снимков,](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) чтобы увидеть состояние исходного кода и переменных в момент отбрасывания.
* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для детального представления производительности и использования приложения.
* Используйте [тесты доступности](../../azure-monitor/app/monitor-web-app-availability.md), чтобы постоянно проверять работу приложения из всех точек мира.
* [Инъекция зависимости в ASP.NET Ядре](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
