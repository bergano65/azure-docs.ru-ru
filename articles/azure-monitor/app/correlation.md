---
title: Корреляция данных телеметрии Azure Application Insights | Документация Майкрософт
description: Корреляция данных телеметрии Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 2e862410e2bf12e09e1a6388bbb6f7105b5b2edf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405264"
---
# <a name="telemetry-correlation-in-application-insights"></a>Корреляция данных телеметрии в Application Insights

В мире микрослужб для каждой логической операции требуется выполнение действий в различных компонентах службы. Вы можете контролировать каждый из этих компонентов отдельно с помощью [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights поддерживает распределенную корреляцию данных телеметрии, позволяющую определить, какой компонент отвечает за сбой или снижение производительности.

В этой статье описывается модель данных, используемая службой Application Insights для корреляции данных телеметрии, отправляемых несколькими компонентами. Рассматриваются методы и протоколы распространения контекста. Он также охватывает внедрение тактики корреляции на различных языках и платформах.

## <a name="data-model-for-telemetry-correlation"></a>Модель корреляции для данных телеметрии

Application Insights определяет [модель данных](../../azure-monitor/app/data-model.md) для распределенной корреляции данных телеметрии. Чтобы связать телеметрию с логической операцией, каждый `operation_Id`элемент телеметрии имеет контекстное поле под названием . Этот идентификатор совместно используется каждым элементом телеметрии в распределенной трассировке. Таким образом, даже если вы потеряете телеметрию из одного слоя, вы все равно можете связать телеметрию, о которых сообщают другие компоненты.

Распределенная логическая операция обычно состоит из набора небольших операций, которые обрабатываются одним из компонентов. Эти операции определяются [телеметрией запросов](../../azure-monitor/app/data-model-request-telemetry.md). Каждый элемент телеметрии `id` запроса имеет свой собственный элемент, который идентифицирует его однозначно и глобально. И все элементы телеметрии (например, следы и исключения), которые связаны с запросом, должны установить `operation_parentId` значение запроса. `id`

Каждая исходящая операция (например, вызов HTTP к другому компоненту) представлена [телеметрией зависимостей](../../azure-monitor/app/data-model-dependency-telemetry.md). Телеметрия зависимостей также `id` определяет свою собственную, которая является глобально уникальной. Телеметрия запросов, инициированная этим вызовом зависимостей, использует `id` в качестве `operation_parentId`.

Вы можете создать представление распределенной логической операции, используя `operation_Id`, `operation_parentId` и `request.id` с `dependency.id`. Эти поля также определяют причинно-следственную связь вызовов телеметрии.

В среде микрослужб трассировки компонентов могут отправляться в разные хранилища. Каждый компонент может иметь собственный ключ инструментирования в Application Insights. Чтобы получить телеметрию для логической операции, Application Insights запрашивает данные из каждого элемента хранения. Когда количество элементов хранения велико, вам понадобится подсказка о том, где искать дальше. Модель данных Application Insights определяет два поля (`request.source` и `dependency.target`) для решения этой проблемы. Первое поле определяет компонент, инициировавав запрос зависимости. Второе поле определяет, какой компонент вернул ответ вызова зависимости.

## <a name="example"></a>Пример

Давайте рассмотрим пример. Приложение под названием Цены на акции показывает текущую рыночную цену акции с помощью внешнего API под названием Stock. Приложение Stock Prices имеет страницу под названием Фондовая `GET /Home/Stock`страница, которую клиент веб-браузер открывает с помощью . Приложение запрашивает Фондовый API с `GET /api/stock/value`помощью вызова HTTP .

Вы можете проанализировать итоговые данные телеметрии, выполнив запрос:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Обратите внимание, что в результатах все элементы телеметрии используют корневой `operation_Id`. При вызове Ajax со страницы назначается`qJSXU`новый уникальный идентификатор () телеметрии зависимости, а идентификатор страницыView используется как `operation_ParentId`. Запрос сервера затем использует идентификатор Ajax как `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| запрос    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

При вызове `GET /api/stock/value` во внешнюю службу необходимо знать личность этого сервера, `dependency.target` чтобы можно было соответствующим образом установить поле. Если внешняя служба не поддерживает мониторинг, то в качестве имени узла службы задается `target` (например, `stock-prices-api.com`). Но если служба идентифицирует себя, вернув заранее определенный заголовок HTTP, содержит идентификатор службы, `target` который позволяет Application Insights создавать распределенный след, задав вопрос телеметрии из этой службы.

## <a name="correlation-headers"></a>Заголовки корреляции

Приложение Исследования переходит на [W3C Trace-Контекст](https://w3c.github.io/trace-context/), который определяет:

- `traceparent`: Носит глобально уникальный идентификатор операции и уникальный идентификатор вызова.
- `tracestate`: Носит контекст отслеживания системы.

Последняя версия SDK Application Insights поддерживает протокол Trace-Context, но, возможно, вам придется выбрать его. (Обратная совместимость с предыдущим протоколом корреляции, поддерживаемым SDK Application Insights, будет сохранена.)

Корреляция [HTTP протокол, также называемый Запрос-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), в настоящее время удаляется. Этот протокол определяет два заголовка:

- `Request-Id`: Носит глобально уникальный идентификатор вызова.
- `Correlation-Context`: Осуществляет коллекцию пар именоемных значений распределенных свойств трассировки.

Приложение Исследования также определяет [расширение](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) для корреляции HTTP протокола. Она использует пары "имя — значение" `Request-Context` для распространения коллекции свойств, используемых непосредственным вызывающим или вызываемым. Приложение Insights SDK использует этот заголовок `request.source` для настройки и полей. `dependency.target`

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Включение поддержки распределенной трассировки W3C для классических приложений ASP.NET
 
  > [!NOTE]
  >  Начиная `Microsoft.ApplicationInsights.Web` с `Microsoft.ApplicationInsights.DependencyCollector`и , не требуется конфигурация.

Поддержка W3C Trace-Context реализуется в обратном порядке. Корреляция, как ожидается, будет работать с приложениями, которые оснащены предыдущими версиями SDK (без поддержки W3C).

Если вы хотите продолжать `Request-Id` использовать устаревший протокол, можно отключить Trace-Context с помощью этой конфигурации:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

При запуске старой версии SDK мы рекомендуем обновить ее или применить следующую конфигурацию для включения Trace-Context.
Эта функция доступна `Microsoft.ApplicationInsights.Web` в `Microsoft.ApplicationInsights.DependencyCollector` пакетах, начиная с версии 2.8.0-beta1.
Она отключена по умолчанию. Чтобы включить его, внести эти изменения в: `ApplicationInsights.config`

- Под, `RequestTrackingTelemetryModule`добавить `EnableW3CHeadersExtraction` элемент и `true`установить его значение .
- Под, `DependencyTrackingTelemetryModule`добавить `EnableW3CHeadersInjection` элемент и `true`установить его значение .
- `W3COperationCorrelationTelemetryInitializer` Добавить `TelemetryInitializers`под . Он будет выглядеть как на этом примере:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Включение поддержки распределенной трассировки W3C для приложений ASP.NET Core

 > [!NOTE]
  > Начиная `Microsoft.ApplicationInsights.AspNetCore` с версии 2.8.0, конфигурация не требуется.
 
Поддержка W3C Trace-Context реализуется в обратном порядке. Корреляция, как ожидается, будет работать с приложениями, которые оснащены предыдущими версиями SDK (без поддержки W3C).

Если вы хотите продолжать `Request-Id` использовать устаревший протокол, можно отключить Trace-Context с помощью этой конфигурации:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

При запуске старой версии SDK мы рекомендуем обновить ее или применить следующую конфигурацию для включения Trace-Context.

Эта функция доступна в `Microsoft.ApplicationInsights.AspNetCore` версии 2.5.0-beta1 и в `Microsoft.ApplicationInsights.DependencyCollector` версии 2.8.0-beta1.
Она отключена по умолчанию. Чтобы ее включить, задайте для `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` значение `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Включение поддержки распределенной трассировки консорциума W3C для приложений Java

#### <a name="java-30-agent"></a>Агент Java 3.0

  Агент Java 3.0 поддерживает W3C из коробки, и дополнительная конфигурация не требуется. 

#### <a name="java-sdk"></a>Пакет SDK для Java
- **Входящая конфигурация**

  - Для приложений Java EE добавьте следующее в `<TelemetryModules>` тег в ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Для приложений Spring Boot добавьте следующие свойства:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Исходящая конфигурация**

  Добавьте к файлу AI-Agent.xml следующее:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > По умолчанию включен режим обратной совместимости, и параметр `enableW3CBackCompat` является необязательным. Используйте его, только если вы хотите отключить режим обратной совместимости.
  >
  > В идеале его можно отключить, когда все ваши службы обновлены до новой версии пакетов SDK, поддерживающих протокол консорциума W3C. Мы настоятельно рекомендуем перейти к этим новым пакетам SDK как можно скорее.

> [!IMPORTANT]
> Убедитесь, что входящие и исходящие конфигурации точно такие же.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Включить поддержку отслеживания W3C для веб-приложений

Эта функция `Microsoft.ApplicationInsights.JavaScript`находится в . Она отключена по умолчанию. Чтобы включить его, используйте `distributedTracingMode` конфигурацию. AI_AND_W3C обеспечивается для обратной совместимости с любыми устаревшими услугами, оперированными Application Insights.

- **npm настройки (игнорировать при использовании установки Фрагмент)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Установка фрагмента (игнорировать при использовании npm установки)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing и Application Insights

[Спецификации модели данных OpenTracing](https://opentracing.io/) и модели данных Application Insights сопоставлены следующим образом:

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span` с `span.kind = server`                    |
| `Dependency`                           | `Span` с `span.kind = client`                    |
| `Id` из `Request` и `Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference` типа `ChildOf` (родительский диапазон)     |

Для получения дополнительной [Application Insights telemetry data model](../../azure-monitor/app/data-model.md)информации см.

Для определения концепций OpenTracing см. [specification](https://github.com/opentracing/specification/blob/master/specification.md) [semantic conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

## <a name="telemetry-correlation-in-opencensus-python"></a>Телеметрическая корреляция в OpenCensus Python

OpenCensus Python `OpenTracing` следует спецификациям модели данных, изложенным ранее. Он также поддерживает [W3C Trace-Context,](https://w3c.github.io/trace-context/) не требуя никакой конфигурации.

### <a name="incoming-request-correlation"></a>Входящие корреляции запроса

OpenCensus Python соотносит заголовки W3C Trace-Context от входящих запросов к диапазонам, генерируемым из самих запросов. OpenCensus будет делать это автоматически с интеграцией для этих популярных инфраструктур веб-приложений: Flask, Django и Pyramid. Вам просто нужно заполнить W3C Trace-Context заголовки с [правильным форматом](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) и отправить их с запросом. Вот пример приложения Флоска, который демонстрирует это:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Этот код запускает пример приложения Флога на `8080`локальной машине, слушая порт. Чтобы соотнести контекст трассировки, вы отправляете запрос в конечную точку. В этом примере можно `curl` использовать команду:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Посмотрев [на формат заголовка Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)можно получить следующую информацию:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Если вы посмотрите на запись запроса, отправленную в Azure Monitor, можно увидеть поля, заполненные информацией о заголовке трассировки. Вы можете найти эти данные в журнале Журналы (Аналитика) в ресурсе Azure Monitor Application Insights.

![Запрос телеметрии в журналах (аналитика)](./media/opencensus-python/0011-correlation.png)

Поле `id` находится в `<trace-id>.<span-id>`формате, `trace-id` где берется из заголовка трассировки, `span-id` который был пройден в запросе, и является генерируемым массивом 8 байт для этого диапазона.

Поле `operation_ParentId` находится в `<trace-id>.<parent-id>`формате, `trace-id` где `parent-id` и и взяты из заголовка трассировки, который был пройден в запросе.

### <a name="log-correlation"></a>Корреляция журналов

OpenCensus Python позволяет соотнести журналы, добавив идентификатор трассировки, идентификатор диапазона и флаг выборки для записей журналов. Вы добавляете эти атрибуты, установив [интеграцию регистрации](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Следующие атрибуты будут добавлены `traceId`к `spanId`объектам `traceSampled`Python: `LogRecord` , и . Обратите внимание, что это вступает в силу только для регистраторов, которые создаются после интеграции.

Вот пример приложения, которое демонстрирует это:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
При запуске этого кода в консоли приводятся следующие отпечатки:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Обратите внимание, что `spanId` есть подарок для сообщения журнала, которое находится в пределах диапазона. Это то `spanId` же самое, что `hello`принадлежит к диапазону имени .

Вы можете экспортировать данные журнала с помощью `AzureLogHandler`. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Корреляция данных телеметрии в .NET

Со временем .NET определил несколько способов корреляции телеметрии и журналов диагностики:

- `System.Diagnostics.CorrelationManager`позволяет отслеживать [LogicalOperationStack и ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` и трассировка событий Windows(ETW) определяет метод [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger`использует [области журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) и HTTP подключают распространение "текущего" контекста.

Но эти методы не позволяют автоматически распределить поддержку трассировки. `DiagnosticSource`поддерживает автоматическую кросс-машинную корреляцию. Библиотеки .NET поддерживают `DiagnosticSource` и позволяют автоматически распространять контекст корреляции через транспорт, например HTTP.

[Руководство пользователя](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) активности `DiagnosticSource` в объясняет основы отслеживания деятельности.

ASP.NET Core 2.0 поддерживает извлечение заголовков HTTP и начало новых действий.

`System.Net.Http.HttpClient`, начиная с версии 4.1.0, поддерживает автоматическое впрыскивание корреляционных заголовков HTTP и отслеживание http-вызовов как действий.

Там в новый модуль HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), для классических ASP.NET. Этот модуль реализует корреляцию данных телеметрии с помощью `DiagnosticSource`. Он запускает действие, исходя из заголовков входящего запроса. Он также коррелирует телеметрию с различных этапов обработки запросов, даже когда каждый этап обработки Интернет-информации (IIS) выполняется по другому управляемому потоку.

Пакет SDK для Application Insights, начиная с версии 2.4.0-beta1, использует `DiagnosticSource` и `Activity` для сбора данных телеметрии и их связывания с текущим действием.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Телеметрия корреляции в Java

[Java-агент,](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) а также [Java SDK](../../azure-monitor/app/java-get-started.md) версия 2.0.0 или позже поддерживает автоматическую корреляцию телеметрии. Он автоматически `operation_id` заполняется для всех телеметрии (например, следов, исключений и пользовательских событий), выданных в рамках запроса. Он также распространяет заголовки корреляций (описанные ранее) для вызовов службы в службу через HTTP, если настроен [агент Java SDK.](../../azure-monitor/app/java-agent.md)

> [!NOTE]
> Агент Application Insights Java автоматически собирает запросы и зависимости для JMS, Kafka, Netty/Webflux и многое другое. Для Java SDK только звонки, сделанные через Apache HttpClient поддерживаются для функции корреляции. Автоматическое распространение контекста в технологиях обмена сообщениями (таких как Kafka, RabbitM и Azure Service Bus) не поддерживается в SDK. 

> [!NOTE]
> Для сбора пользовательской телеметрии необходимо инструментировать приложение с Java 2.6 SDK. 

### <a name="role-names"></a>Имена ролей

Возможно, вы захотите настроить способ отображения имен компонентов в [application Map.](../../azure-monitor/app/app-map.md) Для этого можно вручную установить `cloud_RoleName` одно из следующих действий:

- Для агента Application Insights Java 3.0 установите имя роли облака следующим образом:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Вы также можете установить имя роли `APPLICATIONINSIGHTS_ROLE_NAME`облака с помощью переменной среды.

- С Application Insights Java SDK 2.5.0 и `cloud_RoleName` позже, вы можете указать, добавив `<RoleName>` в файл ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Если вы используете Spring Boot с приложением Insights Spring Boot Starter, вам просто нужно установить пользовательское имя для приложения в файле application.properties:

  `spring.application.name=<name-of-app>`

  Стартер Весенней загрузки `cloudRoleName` автоматически присваивает значение, вписанное для `spring.application.name` свойства.

## <a name="next-steps"></a>Дальнейшие действия

- Напишите [пользовательские телеметрии](../../azure-monitor/app/api-custom-events-metrics.md).
- Для продвинутых сценариев корреляции [Track custom operations](custom-operations-tracking.md)в ASP.NET Core и ASP.NET см.
- Узнайте об [установке свойства cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) для других пакетов SDK.
- Подключите все компоненты своей микрослужбы с помощью Application Insights. Ознакомьтесь со сведениями о [поддерживаемых платформах](../../azure-monitor/app/platforms.md).
- В [этой статье](../../azure-monitor/app/data-model.md) представлена модель данных для Application Insights.
- Вы можете узнать, как [расширять и фильтровать данные телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
- Просмотрите [справочник по конфигурации в Application Insights](configuration-with-applicationinsights-config.md).
