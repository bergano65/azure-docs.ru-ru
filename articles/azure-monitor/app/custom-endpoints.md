---
title: Azure Monitor - Azure Application Insights в переопределите конечные точки по умолчанию SDK | Документация Майкрософт
description: Измените конечные точки по умолчанию пакет SDK Azure Application Insights для областей, таких как Azure для государственных организаций.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: d086815373b84c0f2a70144a505108875fc04981
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443327"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Переопределение конечных точек по умолчанию Application Insights

Для отправки данных из Application Insights в определенных регионах, необходимо переопределить адреса конечных точек по умолчанию. Каждый пакет SDK требует немного различные изменения, все они описаны в этой статье. Эти изменения требуют настройки в примере кода и заменив значения заполнителей `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address`, и `Profile_Query_Endpoint_address` с адресами фактической конечной точки для вашего конкретного региона. Этой статье содержатся ссылки на адреса конечных точек для регионов, где эта настройка необходима.

## <a name="sdk-code-changes"></a>Изменения кода в пакете SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET с использованием файла applicationinsights.config

> [!NOTE]
> Файл applicationinsights.config автоматически перезаписывается каждый раз, когда выполняется обновление пакета SDK. После выполнения обновления пакета SDK обязательно введите значения конкретной конечной точки, регион.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="net-core"></a>.NET Core

Измените файл appsettings.json в проекте таким образом, чтобы настроить основной конечной точки:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Значения для Live Metrics и конечная точка запроса профиля можно задать только с помощью кода. Чтобы переопределить значения по умолчанию для всех значений конечной точки с помощью кода, внесите следующие изменения в `ConfigureServices` метод `Startup.cs` файла:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

Измените файл applicationinsights.xml, чтобы изменить адрес конечной точки по умолчанию.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Изменить `application.properties` файл и добавьте:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Также можно настроить конечные точки посредством переменных среды:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Области, которые требуют изменения конечной точки

В настоящее время единственный регион, потребовать внесения изменений в конечной точке, [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) и [Китая](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Регион |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | Канал телеметрии | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure для Китая | QuickPulse (Live Metrics) |`https://quickpulse.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure для Китая | Запрос профиля |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Канал телеметрии |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Live Metrics) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Запрос профиля |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

> [!NOTE]
> На основе предоставления без помощи кода/расширение агента мониторинга для служб приложений Azure — **в настоящее время не поддерживается** в этих регионах. Как только эта функция станет доступным в этой статье будет обновляться.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о пользовательских изменений для Azure для государственных организаций, можно получить подробное описание [Azure мониторинга и управления](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Чтобы узнать больше о Azure для Китая, обратитесь к [сборник тренировочных заданий для Azure для Китая](https://docs.microsoft.com/azure/china/).