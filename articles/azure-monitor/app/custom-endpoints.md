---
title: Azure Monitor-Azure Application Insights переопределить конечные точки пакета SDK по умолчанию | Документация Майкрософт
description: Измените конечные точки Azure Application Insights SDK по умолчанию для регионов, таких как Azure для государственных организаций.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: mbullwin
ms.openlocfilehash: 6c53a1ff31cb6af6c43f30a4f6fcfdd98f94dab6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814260"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Application Insights переопределение конечных точек по умолчанию

Чтобы отправить данные из Application Insights в определенные регионы, необходимо переопределить адреса конечных точек по умолчанию. Каждый пакет SDK требует немного других изменений, все из которых описаны в этой статье. Эти изменения потребует настройки образца кода и замены значений заполнителей для `QuickPulse_Endpoint_Address`, `TelemetryChannel_Endpoint_Address`и `Profile_Query_Endpoint_address` с фактическими адресами конечных точек для конкретного региона. В конце этой статьи содержатся ссылки на адреса конечных точек для регионов, где требуется такая конфигурация.

## <a name="sdk-code-changes"></a>Изменения кода пакета SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET с applicationinsights. config

> [!NOTE]
> Файл applicationinsights. config автоматически перезаписывается в любое время, когда выполняется обновление пакета SDK. После обновления пакета SDK необходимо повторно ввести значения конечной точки, относящиеся к региону.

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

Измените файл appSettings. JSON в проекте следующим образом, чтобы настроить основную конечную точку.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Значения динамических метрик и конечной точки запроса профиля можно задать только с помощью кода. Чтобы переопределить значения по умолчанию для всех значений конечных точек с помощью кода, внесите следующие `ConfigureServices` изменения в метод `Startup.cs` файла:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

Измените файл applicationinsights. XML, чтобы изменить адрес конечной точки по умолчанию.

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

`application.properties` Измените файл и добавьте:

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

Конечные точки можно также настроить с помощью переменных среды:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Регионы, требующие изменения конечной точки

Сейчас только регионы, требующие внесения изменений в конечную точку, — это [Azure для государственных организаций](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) и [Azure для Китая](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Район |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | Канал телеметрии | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure для Китая | Куиккпулсе (динамические метрики) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure для Китая | Запрос профиля |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure для государственных организаций | Канал телеметрии |`https://dc.applicationinsights.us/v2/track` |
| Azure для государственных организаций | Куиккпулсе (динамические метрики) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure для государственных организаций | Запрос профиля |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Если в настоящее время используется [Application Insights REST API](https://dev.applicationinsights.io/
) , доступ к которому обычно осуществляется через "API.applicationinsights.IO", необходимо использовать конечную точку, которая является локальной для вашего региона:

|Район |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | REST API | `api.applicationinsights.azure.cn` |
| Azure для государственных организаций | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Мониторинг на основе модуля и агента без кода для служб приложений Azure **сейчас не поддерживается** в этих регионах. Как только эта функция станет доступна, эта статья будет обновлена.

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о настраиваемых изменениях для Azure для государственных организаций см. в подробных руководствах по [мониторингу и управлению Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Дополнительные сведения о Azure для Китая см. в [сборник тренировочных заданийе Azure для Китая](https://docs.microsoft.com/azure/china/).
