---
title: Включить отладчик моментальных снимков для приложений .NET в Azure Service Fabric, облачной службы и виртуальные машины | Документация Майкрософт
description: Включить отладчик моментальных снимков для приложений .NET в Azure Service Fabric, облачной службы и виртуальные машины
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60783506"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Включить отладчик моментальных снимков для приложений .NET в Azure Service Fabric, облачной службы и виртуальные машины

Если ASP.NET или ASP.NET core приложения выполняется в службе приложений Azure, можно также использовать приведенные ниже инструкции. Если приложению не требуется пользовательскую конфигурацию с отладчиком моментальных снимков, настоятельно рекомендуется для [включить странице на портале Application Insights Snapshot Debugger](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Если приложение выполняется в Azure Service Fabric, облачная служба, виртуальные машины, или локальных компьютеров, следует использовать приведенные ниже инструкции. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Настройка сбора моментальных снимков для приложений

1. [Включите Application Insights в веб-приложении](../../azure-monitor/app/asp-net.md), если вы еще не сделали это.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. При необходимости, Настройка конфигурации отладчика моментальных снимков, добавляемый [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Конфигурация отладчика моментальных снимков по умолчанию практически пуст и все параметры являются необязательными. Ниже приведен пример, показывающий конфигурация соответствует конфигурации по умолчанию:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. В некоторых случаях (например, в более старых версиях платформы .NET) может потребоваться [настроить сбор исключений](../../azure-monitor/app/asp-net-exceptions.md#exceptions), чтобы просматривать исключения с помощью моментальных снимков, которые отображаются на портале.


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Настройка сбора моментальных снимков для приложений ASP.NET Core 2.0

1. [Включите Application Insights в веб-приложении ASP.NET Core](../../azure-monitor/app/asp-net-core.md), если вы еще не сделали это.

    > [!NOTE]
    > Убедитесь, что приложение ссылается на версию 2.1.1 или более позднюю версию пакета Microsoft.ApplicationInsights.AspNetCore.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Чтобы добавить и настроить обработчик телеметрии сборщика моментальных снимков, измените класс `Startup` своего приложения.

    Добавьте указанные ниже операторы using в `Startup.cs`.

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Добавьте указанный ниже класс `SnapshotCollectorTelemetryProcessorFactory` в класс `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Добавьте службы `SnapshotCollectorConfiguration` и `SnapshotCollectorTelemetryProcessorFactory` в конвейер запуска:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. При необходимости настроить конфигурацию отладчика моментальных снимков, добавив раздел snapshotcollectorconfiguration в файл appsettings.json. Все параметры в конфигурации отладчика моментальных снимков являются необязательными. Ниже приведен пример, показывающий конфигурация соответствует конфигурации по умолчанию:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Настройка сбора моментальных снимков для других приложений .NET

1. Если приложение все еще не инструментировано с помощью Application Insights, начните с [включения Application Insights и установки ключа инструментирования](../../azure-monitor/app/windows-desktop.md).

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Моментальные снимки собираются только для исключений, которые передаются в Application Insights. Для их передачи может потребоваться изменить код. Код обработки исключений зависит от структуры приложения. Пример приведен ниже:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Дальнейшие действия

- Создайте трафик для приложения, которое может вызвать исключение. Подождите 10 – 15 минут для моментальных снимков, который должны отправляться экземпляра Application Insights.
- См. в разделе [моментальные снимки](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) на портале Azure.
- Помощь в устранении неполадок Profiler см. в разделе [Устранение неполадок Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
