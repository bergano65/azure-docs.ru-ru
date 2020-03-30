---
title: Исследования приложений Azure переопределяют конечные точки SDK по умолчанию
description: Изменение конечных точек приложения Azure Monitor Для таких регионов, как Azure Government, измените конечные точки приложения Azure Monitor.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b4ab05c7ee815b385ffb2d1ff9e621063d744dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298316"
---
# <a name="application-insights-overriding-default-endpoints"></a>Анализ приложений переопределяет конечные точки по умолчанию

Для отправки данных из Application Insights в определенные регионы необходимо переопределить адреса конечных точек по умолчанию. Каждый SDK требует несколько различных изменений, все из которых описаны в этой статье. Эти изменения требуют корректировки кода выборки и `QuickPulse_Endpoint_Address`замены значений заполнителя для, `TelemetryChannel_Endpoint_Address`и `Profile_Query_Endpoint_address` с фактическими адресами конечных точек для вашего конкретного региона. Конец этой статьи содержит ссылки на конечные адреса для регионов, где требуется эта конфигурация.

## <a name="sdk-code-changes"></a>Изменения кода SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET с applicationinsights.config

> [!NOTE]
> Файл applicationinsights.config автоматически перезаписываются в любое время выполнения обновления SDK. После выполнения обновления SDK обязательно введите определенные значения конечной точки региона.

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

### <a name="aspnet-core"></a>ASP.NET Core

Изменяйте файл appsettings.json в проекте следующим образом, чтобы настроить основную конечную точку:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Значения для Live Metrics и конечная точка запроса профиля могут быть установлены только с помощью кода. Чтобы переопределить значения по умолчанию для всех значений конечной `ConfigureServices` точки с `Startup.cs` помощью кода, внести следующие изменения в метод файла:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Функции Azure v2.x

Установите следующие пакеты в свой функциональный проект:

- Microsoft.ApplicationInsights версия 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector версия 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel версия 2.10.0

Затем добавьте (или измените) код запуска для приложения функции:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

Измените файл applicationinsights.xml, чтобы изменить адрес конечных точек по умолчанию.

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

Измените `application.properties` файл и добавьте:

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

Конечные точки также могут быть настроены с помощью переменных среды:

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

### <a name="python"></a>Python

Для получения рекомендаций по изменению точки приема для Opencensus-питон SDK обратитесь к [opencensus-питон репо.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

## <a name="regions-that-require-endpoint-modification"></a>Регионы, требующие модификации конечных точек

В настоящее время единственными регионами, требующими модификации конечных точек, являются [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) и [Azure China.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|Регион |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | Канал телеметрии | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure для Китая | БыстрыйПульс (прямая метрика) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure для Китая | Профильный запрос |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure для государственных организаций | Канал телеметрии |`https://dc.applicationinsights.us/v2/track` |
| Azure для государственных организаций | БыстрыйПульс (прямая метрика) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure для государственных организаций | Профильный запрос |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Если в настоящее время используется [API Application Insights REST,](https://dev.applicationinsights.io/
) к которому обычно обращаются через "api.applicationinsights.io", вам необходимо использовать локальную точку, которая является локальной для вашего региона:

|Регион |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | REST API | `api.applicationinsights.azure.cn` |
| Azure для государственных организаций | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Мониторинг на основе бескодатого агента/расширения для служб приложений Azure в **настоящее время не поддерживается** в этих регионах. Как только эта функциональность станет доступна, эта статья будет обновлена.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о пользовательских модификациях для правительства Azure, обратитесь к подробным рекомендациям по [мониторингу и управлению Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
- Чтобы узнать больше о Azure China, обратитесь в [журнал Azure China Playbook.](https://docs.microsoft.com/azure/china/)
