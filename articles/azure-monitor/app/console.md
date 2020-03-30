---
title: Azure Application Insights для консольных приложений | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений.
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655435"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights для консольных приложений .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) позволяет отслеживать доступность, производительность и использование вашего веб-приложения.

Вам потребуется подписка [Microsoft Azure](https://azure.com). Войдите с помощью учетной записи Майкрософт, которую вы, возможно, уже используете для Windows, XBox Live или других облачных служб Майкрософт. Возможно, у вашей группы есть подписка организации Azure: попросите ее владельца добавить вас к ней с помощью вашей учетной записи Майкрософт.

> [!NOTE]
> Существует новое приложение Исследования SDK называется [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) который может использоваться для включения приложения Исследования для любых консольных приложений. Рекомендуется использовать этот пакет и связанные с ним инструкции [отсюда](../../azure-monitor/app/worker-service.md). Этот пакет [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)цели, и, следовательно, могут быть использованы в .NET Core 2.0 или выше, и .NET Framework 4.7.2 или выше.

## <a name="getting-started"></a>Начало работы

* На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights.](../../azure-monitor/app/create-new-resource.md) Для параметра типа приложения выберите приложение **Общий**.
* Сделайте копию ключа инструментирования. Найдите ключ в **Essentials** отсева нового ресурса, который вы создали.
* Установите последнюю версию пакета [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights).
* Прежде чем отслеживать телеметрию, задайте ключ инструментирования в коде или переменную среды APPINSIGHTS_INSTRUMENTATIONKEY. После этого вы сможете отслеживать телеметрию вручную и просматривать данные телеметрии на портале Azure.

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Телеметрия не отправляется мгновенно. Элементы телеметрии отослаиваются и отправляются SDK ApplicationInsights. В приложениях Console, которые `Track()` выходят сразу после вызова методов, телеметрия не может быть отправлена, если `Flush()` и `Sleep` не будет сделано до выхода приложения, как показано в полном [примере](#full-example) позже в этой статье.


* Установите последнюю версию пакета [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector), который позволяет автоматически отслеживать HTTP-вызовы, SQL-вызовы или другие внешние вызовы зависимостей.

Вы можете инициализировать и настроить Application Insights с помощью кода или файла `ApplicationInsights.config`. Выполните инициализацию как можно раньше. 

> [!NOTE]
> Инструкции для **ApplicationInsights.config** применимы только к приложениям, которые нацелены на платформу .NET Framework, и не подходят для платформы .NET Core.

### <a name="using-config-file"></a>Использование файла конфигурации

По умолчанию пакет SDK для Application Insights ищет файл `ApplicationInsights.config` в рабочей папке при создании `TelemetryConfiguration`.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Вы также можете указать путь к файлу конфигурации.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Дополнительные сведения см. в [справочных материалах по файлу конфигурации](configuration-with-applicationinsights-config.md).

Чтобы получить полный пример файла конфигурации, установите последнюю версию пакета [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). В этом руководстве приводится **минимальная** конфигурация для коллекции зависимостей, как в примере кода ниже.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Настройка сбора данных телеметрии с помощью кода
> [!NOTE]
> Чтение файла конфигурации не поддерживается на .NET Core. Вы можете рассмотреть возможность использования [Application Insights SDK для ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* При запуске приложения создайте и настройте экземпляр `DependencyTrackingTelemetryModule`. Он должен быть одноэлементным и сохраняться в течение всего времени существования приложения.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Добавьте общие инициализаторы телеметрии.

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Если вы создали `TelemetryConfiguration()` конфигурацию с простым конструктором, необходимо дополнительно включить поддержку корреляции. **Это не нужно,** если вы читаете конфигурацию из файла, используется `TelemetryConfiguration.CreateDefault()` или `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Вы также можете установить и инициализировать модуль коллектора Performance Counter, описанный [здесь](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Полный пример

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживайте зависимости](../../azure-monitor/app/asp-net-dependencies.md), чтобы выяснить, что стало причиной медленной работы — REST, SQL или другие внешние ресурсы.
* [Используйте API](../../azure-monitor/app/api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
