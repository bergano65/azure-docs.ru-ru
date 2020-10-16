---
title: Azure Application Insights переопределить конечные точки пакета SDK по умолчанию
description: Измените Azure Monitor по умолчанию конечные точки пакета SDK Application Insights для регионов, таких как Azure для государственных организаций.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: d6cea9044cd4898480fcc30532a05e6c8a407012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333296"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights переопределение конечных точек по умолчанию

Чтобы отправить данные из Application Insights в определенные регионы, необходимо переопределить адреса конечных точек по умолчанию. Каждый пакет SDK требует немного других изменений, все из которых описаны в этой статье. Эти изменения потребует настройки образца кода и замены значений заполнителей для `QuickPulse_Endpoint_Address` , `TelemetryChannel_Endpoint_Address` и `Profile_Query_Endpoint_address` с фактическими адресами конечных точек для конкретного региона. В конце этой статьи содержатся ссылки на адреса конечных точек для регионов, где требуется такая конфигурация.

> [!NOTE]
> [Строки подключения](./sdk-connection-string.md?tabs=net) — это новый предпочтительный метод настройки пользовательских конечных точек в Application Insights.

---

## <a name="sdk-code-changes"></a>Изменения кода пакета SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Файл applicationinsights.config автоматически перезаписывается в любое время, когда выполняется обновление пакета SDK. После обновления пакета SDK необходимо повторно ввести значения конечной точки, относящиеся к региону.

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Измените appsettings.jsфайла в проекте следующим образом, чтобы настроить основную конечную точку.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Значения динамических метрик и конечной точки запроса профиля можно задать только с помощью кода. Чтобы переопределить значения по умолчанию для всех значений конечных точек с помощью кода, внесите следующие изменения в `ConfigureServices` метод `Startup.cs` файла:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Функции Azure](#tab/functions)

Для функций Azure теперь рекомендуется использовать [строки подключения](./sdk-connection-string.md?tabs=net) , заданные в параметрах приложения функции. Чтобы получить доступ к параметрам приложения для функции, в области функции выберите **Параметры**  >  **Конфигурация**  >  **приложения параметры**. 

Имя: `APPLICATIONINSIGHTS_CONNECTION_STRING` значение: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

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

Измените `application.properties` файл и добавьте:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

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

# <a name="javascript"></a>[JavaScript](#tab/js)

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

# <a name="python"></a>[Python](#tab/python)

Инструкции по изменению конечной точки приема для пакета SDK для опенценсус-Python см [. в репозитории опенценсус-Python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Регионы, требующие изменения конечной точки

Сейчас только регионы, требующие внесения изменений в конечную точку, — это [Azure для государственных организаций](../../azure-government/compare-azure-government-global-azure.md#application-insights) и [Azure для Китая](/azure/china/resources-developer-guide).

|Регион |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | Канал телеметрии | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure для Китая | Куиккпулсе (динамические метрики) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure для Китая | Запрос профиля |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure для государственных организаций | Канал телеметрии |`https://dc.applicationinsights.us/v2/track` |
| Azure для государственных организаций | Куиккпулсе (динамические метрики) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure для государственных организаций | Запрос профиля |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Если в настоящее время используется [Application Insights REST API](https://dev.applicationinsights.io/
) , доступ к которому обычно осуществляется через "API.applicationinsights.IO", необходимо использовать конечную точку, которая является локальной для вашего региона:

|Регион |  Имя конечной точки | Значение |
|-----------------|:------------|:-------------|
| Azure для Китая | REST API | `api.applicationinsights.azure.cn` |
| Azure для государственных организаций | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Мониторинг на основе модуля и агента без кода для служб приложений Azure **сейчас не поддерживается** в этих регионах. Как только эта функция станет доступна, эта статья будет обновлена.

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о настраиваемых изменениях для Azure для государственных организаций см. в подробных руководствах по [мониторингу и управлению Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights).
- Дополнительные сведения о Azure для Китая см. в [сборник тренировочных заданийе Azure для Китая](/azure/china/).
