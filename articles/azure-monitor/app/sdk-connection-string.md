---
title: Строки подключения в Azure Application Insights (ru) Документы Майкрософт
description: Как использовать строки соединения.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 25eda0ae2b0d873fe9850e5b886489a5f2590e69
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410613"
---
# <a name="connection-strings"></a>Строки подключения

## <a name="overview"></a>Обзор

Строки подключения предоставляют пользователям Application Insight единую настройку конфигурации, устраняя необходимость в нескольких настройках прокси. Очень полезно для веб-серверов интрасети, суверенных или гибридных облачных сред, желающих отправить данные в службу мониторинга.

Пары ключевых значений обеспечивают пользователям простой способ определить комбинацию суффиксов префикса для каждого сервиса/продукта Application Insights (AI).

> [!IMPORTANT]
> Мы не рекомендуем устанавливать ключ подключения и инструментария. В случае, если пользователь устанавливает оба, в зависимости от того, что было установлено последним, будет иметь приоритет. 


## <a name="scenario-overview"></a>Обзор сценария 

Сценарии клиентов, где мы визуализировать это, имеющие наибольшее влияние:

- Исключения брандмауэра или перенаправления прокси 

    В тех случаях, когда требуется мониторинг веб-сервера интрасети, наше предыдущее решение просило клиентов добавить в конфигурацию отдельные конечные точки обслуживания. Дополнительные сведения см. [здесь](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Строки подключения предлагают лучшую альтернативу, сократив эти усилия до одного параметра. Простая приставка, поправка суффикса позволяет автоматически население и перенаправление всех конечных точек на правильные услуги. 

- Суверенные или гибридные облачные среды

    Пользователи могут отправлять данные в определенный [правительственный регион Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
    Строки подключения позволяют определить настройки конечных точек для серверов интрасети или гибридных настроек облака. 

## <a name="getting-started"></a>Начало работы

### <a name="finding-my-connection-string"></a>Поиск строки связи?

Строка соединения отображается на лезвии «Обзор» ресурса Application Insights.

![строка соединения на лезвии обзора](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>схема

#### <a name="max-length"></a>Максимальная длина

Соединение имеет максимальную поддерживаемую длину 4096 символов.

#### <a name="key-value-pairs"></a>Пары "ключ-значение"

Строка соединения состоит из списка параметров, представленных в виде пар ключей, разделенных запятой:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Синтаксис

- `InstrumentationKey`(наве: 00000000-0000-0000-0000-0000000000000000000000000000)  Строка соединения является **необходимым** полем.
- `Authorization`(напротив: ikey) (Эта настройка не является обязательной, потому что сегодня мы поддерживаем только разрешение ikey.)
- `EndpointSuffix`(напротив: applicationinsights.azure.cn) Установка суффикса конечных точек поручает SDK, к которому облако Azure подключиться. SDK соберет остальную конечную точку для отдельных служб.
- Явные конечные точки.
  Любая служба может быть явно переопределена в строке соединения.
   - `IngestionEndpoint`(наве- напротив:https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(наве- напротив:https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(наве- напротив:https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(наве- напротив:https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Схема конечных точек

`<prefix>.<suffix>`
- Префикс: Определяет службу. 
- Суффикс: Определяет общее доменное имя.

##### <a name="valid-suffixes"></a>Действительные суффиксы

Вот список действительных суффиксов 
- applicationinsights.azure.cn
- applicationinsights.us


Дополнительные сведения см. по ссылке https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Действительные префиксы

- [Телеметрическая проглатка](./app-insights-overview.md):`dc`
- [Прямая метрики](./live-stream.md):`live`
- [Профайлер](./profiler-overview.md):`profiler`
- [Моментальный снимок](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Примеры строки подключения


### <a name="minimal-valid-connection-string"></a>Минимальная допустимая строка соединения

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

В этом примере был установлен только ключ инструментария.

- Схема авторизации по умолчанию "ikey" 
- Ключ прибора: 00000000-0000-0000-0000-000000000000000000000000000
- Региональные сервисы URIs основаны на [по умолчанию SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) и будут подключаться к общедоступному глобальному Azure:
   - Приеме внутрь:https://dc.services.visualstudio.com/
   - Показатели в реальном времени:https://rt.services.visualstudio.com/
   - Профилировщик:https://agent.azureserviceprofiler.net/
   - Отладчик:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Строка соединения с суффиксом конечных точек

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

В этом примере эта строка соединения определяет суффикс конечных точек, а SDK будет строить конечные точки обслуживания.

- Схема авторизации по умолчанию "ikey" 
- Ключ прибора: 00000000-0000-0000-0000-000000000000000000000000000
- Региональные услуги URIs основаны на предоставленных конечных суффиксов: 
   - Приеме внутрь:https://dc.ai.contoso.com
   - Показатели в реальном времени:https://live.ai.contoso.com
   - Профилировщик:https://profiler.ai.contoso.com 
   - Отладчик:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Строка соединения с явными переопределениями конечных точек 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

В этом примере эта строка соединения определяет явные переопределения для каждой службы. SDK будет использовать точные конечные точки, предоставляемые без изменений.

- Схема авторизации по умолчанию "ikey" 
- Ключ прибора: 00000000-0000-0000-0000-000000000000000000000000000
- Региональные сервисные URIs основаны на явных значениях переопределения: 
   - Заглатывает: https:\//custom.com:111/
   - Показатели в реальном\/времени: https: /custom.com:222/
   - Профиль: https:\//custom.com:333/ 
   - Отладчик: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Как установить строку соединения

Строки подключения поддерживаются в следующих версиях SDK:
- .NET и .NET Core v2.12.0
- Java v2.5.1 и Java 3.0
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Строка соединения может быть установлена либо в коде, переменной среды или файле конфигурации.



### <a name="environment-variable"></a>Переменная среды

- Строка подключения: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

ТелеметрияКонфигурация.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Явно установить:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Файл config .Net:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) Явно установите:
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

Важно: Javascript не поддерживает использование переменных среды.

Использование фрагмента:

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

Мы рекомендуем пользователям установить переменную среды.

Для четкого настройки строки соединения:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Следующие шаги

Приступите к работе во время выполнения с помощью:

* [Приложения, размещенные в IIS на виртуальной машине Azure или в масштабируемом наборе виртуальных машин Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [сервера IIS;](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Веб-приложения Azure](../../azure-monitor/app/azure-web-apps.md)

Приступите к работе во время разработки с помощью:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
