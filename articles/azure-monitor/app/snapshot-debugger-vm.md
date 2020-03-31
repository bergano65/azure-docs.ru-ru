---
title: Включить снимок Debugger для приложений .NET в Azure Service Fabric, Облачный сервис и виртуальные машины Документы Майкрософт
description: Включить моментальный снимок debugger для приложений .NET в Azure Service Fabric, Облачном сервисе и виртуальных машинах
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671353"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Включить моментальный снимок debugger для приложений .NET в Azure Service Fabric, Облачном сервисе и виртуальных машинах

Если ASP.NET или ASP.NET основное приложение работает в Службе приложений Azure, настоятельно рекомендуется [включить Snapshot Debugger через страницу портала Application Insights.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) Однако, если ваше приложение требует настраиваемый моментальный настройки отладки или предварительной версии ядра .NET, то эта инструкция должна следовать ***в дополнение*** к инструкциям для включения [через страницу портала Application Insights.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)

Если приложение работает в Azure Service Fabric, Cloud Service, Virtual Machines или на местных машинах, следует использовать следующие инструкции. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Настройка сбора моментальных снимков для приложений

1. [Включите Application Insights в веб-приложении](../../azure-monitor/app/asp-net.md), если вы еще не сделали это.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. При необходимости настраивайте конфигурацию Snapshot Debugger, добавленную [в ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md) Конфигурация Snapshot Debugger по умолчанию в основном пуста, и все настройки необязательны. Вот пример, показывающий конфигурацию, эквивалентную конфигурации по умолчанию:

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


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>Настройка коллекции моментальных снимков для приложений с использованием ASP.NET Core 2.0 или выше

1. [Включите Application Insights в веб-приложении ASP.NET Core](../../azure-monitor/app/asp-net-core.md), если вы еще не сделали это.

    > [!NOTE]
    > Убедитесь, что приложение ссылается на версию 2.1.1 или более позднюю версию пакета Microsoft.ApplicationInsights.AspNetCore.

2. Добавьте в приложение пакет NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector).

3. Чтобы добавить и настроить обработчик телеметрии сборщика моментальных снимков, измените класс `Startup` своего приложения.
    1. Если используется версия пакета [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 1.3.5 или выше, `Startup.cs`добавьте следующие операторы с помощью.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       Добавьте следующее в конце метода ConfigureServices в `Startup` классе в `Startup.cs`.

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. Если используется версия пакета [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 1.3.4 или ниже, `Startup.cs`добавьте следующие операторы с помощью.

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

4. При необходимости настроили конфигурацию Snapshot Debugger, добавив раздел SnapshotCollectorConfiguration в appsettings.json. Все настройки в конфигурации Snapshot Debugger не являются обязательными. Вот пример, показывающий конфигурацию, эквивалентную конфигурации по умолчанию:

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

- Создание трафика в приложение, которое может вызвать исключение. Затем подождите от 10 до 15 минут, чтобы снимки были отправлены в экземпляр Application Insights.
- [Снимки](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) можно увидеть на портале Azure.
- Для получения помощи в устранении [Snapshot Debugger troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)проблем с устранением неполадок Snapshot Debugger см.
