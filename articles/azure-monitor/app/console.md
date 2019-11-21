---
title: Azure Application Insights для консольных приложений | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2019
ms.reviewer: lmolkova
ms.openlocfilehash: ee8dabcc957364bade36608067aad568662c24ae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232723"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights для консольных приложений .NET

[Application Insights](../../azure-monitor/app/app-insights-overview.md) позволяет отслеживать доступность, производительность и использование вашего веб-приложения.

Вам потребуется подписка [Microsoft Azure](https://azure.com). Войдите с помощью учетной записи Майкрософт, которую вы, возможно, уже используете для Windows, XBox Live или других облачных служб Майкрософт. Возможно, у вашей группы есть подписка организации Azure: попросите ее владельца добавить вас к ней с помощью вашей учетной записи Майкрософт.

> [!NOTE]
> There is a new Application Insights SDK called [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) which can used to enable Application Insights for any Console Applications. It is recommended to use this package and associated instructions from [here](../../azure-monitor/app/worker-service.md). This package targets [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard), and hence can be used in .NET Core 2.0 or higher, and .NET Framework 4.7.2 or higher.

## <a name="getting-started"></a>Начало работы

* На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights](../../azure-monitor/app/create-new-resource.md). Для параметра типа приложения выберите приложение **Общий**.
* Сделайте копию ключа инструментирования. Найдите ключ в раскрывающемся списке **Основные компоненты** нового ресурса, который вы создали. 
* Установите последнюю версию пакета [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights).
* Прежде чем отслеживать телеметрию, задайте ключ инструментирования в коде или переменную среды APPINSIGHTS_INSTRUMENTATIONKEY. После этого вы сможете отслеживать телеметрию вручную и просматривать данные телеметрии на портале Azure.

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

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
> Reading config file is not supported on .NET Core. You may consider using [Application Insights SDK for ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

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

If you created configuration with plain `TelemetryConfiguration()` constructor, you need to enable correlation support additionally. **It is not needed** if you read configuration from file, used `TelemetryConfiguration.CreateDefault()` or `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* You may also want to install and initialize Performance Counter collector module as described [here](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
