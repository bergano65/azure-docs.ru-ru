---
title: Строки подключения в Application Insights Azure | Документация Майкрософт
description: Как использовать строки подключения.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335166"
---
# <a name="connection-strings"></a>Строки подключения

## <a name="overview"></a>Обзор

Строки подключения предоставляют пользователям Application Insights один параметр конфигурации, устраняя необходимость в нескольких параметрах прокси-сервера. Очень полезна для веб-серверов в интрасети, независимых или гибридных облачных сред, которые ищут передачу данных в службу мониторинга.

Пары "ключ-значение" предоставляют пользователям простой способ определения префикса суффикса для каждой службы Application Insights (AI) или продукта.

> [!IMPORTANT]
> Не рекомендуется задавать как строку подключения, так и ключ инструментирования. В случае, если пользователь установил оба значения, приоритет будет задается в зависимости от того, какое значение было задано последним. 


## <a name="scenario-overview"></a>Общие сведения о сценарии 

Сценарии клиентов, в которых мы видим, что это имеет наибольшее влияние:

- Исключения брандмауэра или перенаправления прокси-сервера 

    В тех случаях, когда требуется мониторинг веб-сервера интрасети, наше более раннее решение попросило пользователей добавить в конфигурацию отдельные конечные точки служб. Дополнительные сведения см. [здесь](../faq.md#can-i-monitor-an-intranet-web-server). 
    Строки подключения предлагают лучшую альтернативу, уменьшая эти усилия на один параметр. Простой префикс, поправку суффиксов позволяет автоматически выполнять заполнение и перенаправление всех конечных точек в нужные службы. 

- Независимых или гибридные облачные среды

    Пользователи могут передавать данные в определенный [регион Azure для государственных организаций](../../azure-government/compare-azure-government-global-azure.md#application-insights).
    Строки подключения позволяют определить параметры конечной точки для серверов интрасети или параметров гибридного облака. 

## <a name="getting-started"></a>Начало работы

### <a name="finding-my-connection-string"></a>Найти строку подключения?

Строка подключения отобразится в колонке обзора ресурса Application Insights.

![Строка подключения в колонке "Обзор"](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>схема

#### <a name="max-length"></a>Максимальная длина

Максимальная поддерживаемая длина подключения — 4096 символов.

#### <a name="key-value-pairs"></a>Пары "ключ-значение"

Строка подключения состоит из списка параметров, представленных парами "ключ-значение", разделенных точкой с запятой. `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Синтаксис

- `InstrumentationKey` (пример: 00000000-0000-0000-0000-000000000000)  Строка подключения является **обязательным** полем.
- `Authorization` (пример: iKey) (Этот параметр является необязательным, так как сегодня поддерживается только ikeyная авторизация.)
- `EndpointSuffix` (пример: applicationinsights.azure.cn) При настройке суффикса конечной точки будет указано, к какому пакету SDK подключается облако Azure. Пакет SDK будет собирать оставшуюся часть конечной точки для отдельных служб.
- Явные конечные точки.
  Любая служба может быть явно переопределена в строке подключения.
   - `IngestionEndpoint` (пример: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint` (пример: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint` (пример: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint` (пример: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Схема конечной точки

`<prefix>.<suffix>`
- Префикс: определяет службу. 
- Суффикс: определяет общее доменное имя.

##### <a name="valid-suffixes"></a>Допустимые суффиксы

Ниже приведен список допустимых суффиксов. 
- applicationinsights.azure.cn
- applicationinsights.us


Дополнительные сведения см. по ссылке https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Допустимые префиксы

- Прием [телеметрии](./app-insights-overview.md):`dc`
- [Динамические метрики](./live-stream.md): `live`
- [Профилировщик](./profiler-overview.md): `profiler`
- [Моментальный снимок](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Примеры строк подключения


### <a name="minimal-valid-connection-string"></a>Минимальная допустимая строка подключения

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

В этом примере задан только ключ инструментирования.

- По умолчанию используется схема авторизации "iKey" 
- Ключ инструментирования: 00000000-0000-0000-0000-000000000000
- Коды URI региональных служб основаны на [стандартах SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) и будут подключаться к общедоступному глобальному серверу Azure:
   - Приема `https://dc.services.visualstudio.com/`
   - Динамические метрики: `https://rt.services.visualstudio.com/`
   - Profiler `https://agent.azureserviceprofiler.net/`
   - Отладчик `https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Строка подключения с суффиксом конечной точки

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

В этом примере в этой строке подключения указывается суффикс конечной точки, и пакет SDK конструирует конечные точки службы.

- По умолчанию используется схема авторизации "iKey" 
- Ключ инструментирования: 00000000-0000-0000-0000-000000000000
- Коды URI региональных служб основаны на указанном суффиксе конечной точки: 
   - Приема `https://dc.ai.contoso.com`
   - Динамические метрики: `https://live.ai.contoso.com`
   - Profiler `https://profiler.ai.contoso.com`
   - Отладчик `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Строка подключения с явными переопределениями конечной точки 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

В этом примере эта строка подключения указывает явные переопределения для каждой службы. Пакет SDK будет использовать точные указанные конечные точки без изменения.

- По умолчанию используется схема авторизации "iKey" 
- Ключ инструментирования: 00000000-0000-0000-0000-000000000000
- Региональные URI служб основаны на явных значениях переопределения: 
   - Приема `https://custom.com:111/`
   - Динамические метрики: `https://custom.com:222/`
   - Profiler `https://custom.com:333/`
   - Отладчик `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Как задать строку подключения

Строки подключения поддерживаются в следующих версиях пакета SDK:
- .NET и .NET Core v 2.12.0
- Java v 2.5.1 и Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Версия Python версии 1.0.0

Строку подключения можно задать в коде, переменной среды или файле конфигурации.



### <a name="environment-variable"></a>Переменная среды

- Строка подключения: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

Телеметриконфигуратион. ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET явным образом задается:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Файл конфигурации .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore явно задано:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

config.jsNetCore: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (версия 2.5. x) явно задано:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Важно. JavaScript не поддерживает использование переменных среды.

Использование фрагмента кода:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Ручная настройка:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Мы рекомендуем пользователям задать переменную среды.

Чтобы явно задать строку подключения, сделайте следующее:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Дальнейшие действия

Приступите к работе во время выполнения с помощью:

* [Приложения, размещенные в IIS на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure](./azure-vm-vmss-apps.md)
* [сервера IIS;](./monitor-performance-live-website-now.md)
* [Веб-приложения Azure](./azure-web-apps.md)

Приступите к работе во время разработки с помощью:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

