---
title: Корреляция данных телеметрии Azure Application Insights | Документация Майкрософт
description: Корреляция данных телеметрии Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bc73dfb1c4dc77abe0bd135ecf572fa05ddf6322
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951332"
---
# <a name="telemetry-correlation-in-application-insights"></a>Корреляция данных телеметрии в Application Insights

В мире микрослужб для каждой логической операции требуется выполнение действий в различных компонентах службы. Каждый из этих компонентов можно отслеживать отдельно с помощью [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights поддерживает распределенную корреляцию данных телеметрии, позволяющую определить, какой компонент отвечает за сбой или снижение производительности.

В этой статье описывается модель данных, используемая службой Application Insights для корреляции данных телеметрии, отправляемых несколькими компонентами. Рассматриваются методы и протоколы распространения контекста. Он также охватывает реализацию тактики корреляции на разных языках и платформах.

## <a name="data-model-for-telemetry-correlation"></a>Модель корреляции для данных телеметрии

Application Insights определяет [модель данных](../../azure-monitor/app/data-model.md) для распределенной корреляции данных телеметрии. Чтобы связать данные телеметрии с логической операцией, каждый элемент телеметрии имеет поле контекста с именем `operation_Id`. Этот идентификатор совместно используется каждым элементом телеметрии в распределенной трассировке. Поэтому даже если вы потеряли данные телеметрии из одного слоя, вы по-прежнему можете связать данные телеметрии, сообщаемые другими компонентами.

Распределенная логическая операция обычно состоит из набора небольших операций, которые являются запросами, обрабатываемыми одним из компонентов. Эти операции определяются [телеметрией запросов](../../azure-monitor/app/data-model-request-telemetry.md). Каждый элемент телеметрии запроса имеет собственный `id`, который идентифицирует его уникальным и глобальным образом. Все элементы телеметрии (например, трассировки и исключения), связанные с запросом, должны устанавливать `operation_parentId` в значение `id`запроса.

Каждая исходящая операция (например, вызов HTTP к другому компоненту) представлена [телеметрией зависимостей](../../azure-monitor/app/data-model-dependency-telemetry.md). Данные телеметрии зависимостей также определяют собственные `id`, глобально уникальными. Телеметрия запросов, инициированная этим вызовом зависимостей, использует `id` в качестве `operation_parentId`.

Вы можете создать представление распределенной логической операции, используя `operation_Id`, `operation_parentId` и `request.id` с `dependency.id`. Эти поля также определяют причинно-следственную связь вызовов телеметрии.

В среде микрослужб трассировки компонентов могут отправляться в разные хранилища. Каждый компонент может иметь собственный ключ инструментирования в Application Insights. Для получения данных телеметрии для логической операции Application Insights запрашивает данные из каждого элемента хранилища. Если количество элементов хранения велико, вам потребуется подсказка о том, где будет выглядеть следующее. Модель данных Application Insights определяет два поля (`request.source` и `dependency.target`) для решения этой проблемы. Первое поле определяет компонент, который инициировал запрос зависимости. Второе поле определяет, какой компонент вернул ответ на вызов зависимости.

## <a name="example"></a>Пример

Давайте рассмотрим пример. Приложение под названием «цены на акции» показывает текущую стоимость рынка акций с помощью внешнего API, который называется «склад». В приложении «цены на акции» имеется страница «акции», которая открывается веб-браузером клиента с помощью `GET /Home/Stock`. Приложение запрашивает стандартный API-интерфейс с помощью `GET /api/stock/value`вызова HTTP.

Вы можете проанализировать итоговые данные телеметрии, выполнив запрос:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Обратите внимание, что в результатах все элементы телеметрии используют корневой `operation_Id`. При вызове Ajax с страницы новый уникальный идентификатор (`qJSXU`) назначается телеметрии зависимости, а идентификатор pageView используется как `operation_ParentId`. Запрос сервера затем использует идентификатор Ajax как `operation_ParentId`.

| itemType   | name                      | ИД           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| запрос    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

При вызове `GET /api/stock/value` во внешнюю службу необходимо знать удостоверение этого сервера, чтобы можно было задать поле `dependency.target` соответствующим образом. Если внешняя служба не поддерживает мониторинг, то в качестве имени узла службы задается `target` (например, `stock-prices-api.com`). Но если служба идентифицирует себя, возвращая предопределенный заголовок HTTP, `target` содержит удостоверение службы, позволяющее Application Insights создавать распределенную трассировку путем запроса данных телеметрии из этой службы.

## <a name="correlation-headers"></a>Заголовки корреляции

Application Insights переходит в [консорциум W3C Trace-context](https://w3c.github.io/trace-context/), который определяет:

- `traceparent`: содержит глобальный уникальный идентификатор операции и уникальный идентификатор вызова.
- `tracestate`: содержит контекст трассировки, зависящий от системы.

Последняя версия пакета SDK Application Insights поддерживает протокол контекста Trace, но может потребоваться согласие на него. (Будет поддерживаться обратная совместимость с предыдущим протоколом корреляции, поддерживаемым пакетом SDK для Application Insights.)

[Протокол HTTP корреляции, также называемый Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), является устаревшим. Этот протокол определяет два заголовка:

- `Request-Id`: содержит глобальный уникальный идентификатор вызова.
- `Correlation-Context`: содержит коллекцию пар "имя-значение" для свойств распределенной трассировки.

Application Insights также определяет [расширение](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) для протокола HTTP корреляции. Она использует пары "имя — значение" `Request-Context` для распространения коллекции свойств, используемых непосредственным вызывающим или вызываемым. Пакет SDK для Application Insights использует этот заголовок для задания `dependency.target` и `request.source` полей.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Включение поддержки распределенной трассировки W3C для классических приложений ASP.NET
 
  > [!NOTE]
  >  Начиная с `Microsoft.ApplicationInsights.Web` и `Microsoft.ApplicationInsights.DependencyCollector`, настройка не требуется.

W3C Trace — поддержка контекста реализована с обратной совместимостью. Корреляция должна работать с приложениями, оснащенными предыдущими версиями пакета SDK (без поддержки W3C).

Если вы хотите использовать устаревший протокол `Request-Id`, можно отключить контекст трассировки, используя следующую конфигурацию:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

При запуске более старой версии пакета SDK рекомендуется обновить или применить следующую конфигурацию, чтобы включить контекст Trace.
Эта функция доступна в пакетах `Microsoft.ApplicationInsights.Web` и `Microsoft.ApplicationInsights.DependencyCollector`, начиная с версии 2.8.0-Beta1.
Она отключена по умолчанию. Чтобы включить его, внесите следующие изменения в `ApplicationInsights.config`.

- В разделе `RequestTrackingTelemetryModule`добавьте элемент `EnableW3CHeadersExtraction` и задайте для него значение `true`.
- В разделе `DependencyTrackingTelemetryModule`добавьте элемент `EnableW3CHeadersInjection` и задайте для него значение `true`.
- Добавьте `W3COperationCorrelationTelemetryInitializer` в `TelemetryInitializers`. Он будет выглядеть, как в следующем примере:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Включение поддержки распределенной трассировки W3C для приложений ASP.NET Core

 > [!NOTE]
  > Начиная с версии `Microsoft.ApplicationInsights.AspNetCore` 2.8.0, настройка не требуется.
 
W3C Trace — поддержка контекста реализована с обратной совместимостью. Корреляция должна работать с приложениями, оснащенными предыдущими версиями пакета SDK (без поддержки W3C).

Если вы хотите использовать устаревший протокол `Request-Id`, можно отключить контекст трассировки, используя следующую конфигурацию:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

При запуске более старой версии пакета SDK рекомендуется обновить или применить следующую конфигурацию, чтобы включить контекст Trace.

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

- **Входящая конфигурация**

  - Для приложений Java EE добавьте следующий код в тег `<TelemetryModules>` в ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Для приложений с пружинной загрузкой добавьте следующие свойства:

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
> Убедитесь, что входящие и исходящие конфигурации совпадают.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Включить поддержку распределенной трассировки W3C для веб-приложений

Эта функция находится в `Microsoft.ApplicationInsights.JavaScript`. Она отключена по умолчанию. Чтобы включить его, используйте `distributedTracingMode` config. AI_AND_W3C предоставляется для обеспечения обратной совместимости со всеми устаревшими службами, оснащенными Application Insights.

- **Установка NPM (пропуск при использовании программы установки фрагмента кода)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Настройка фрагмента кода (не учитывать при использовании программы установки NPM)**

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

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` с `span.kind = server`                  |
| `Dependency`                          | `Span` с `span.kind = client`                  |
| `Id` из `Request` и `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` типа `ChildOf` (родительский диапазон)   |

Дополнительные сведения см. в разделе [Application Insightsная модель данных телеметрии](../../azure-monitor/app/data-model.md).

Определения концепций ОпентраЦинг см. в описании [спецификации](https://github.com/opentracing/specification/blob/master/specification.md) опентраЦинг и [семантических соглашений](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Корреляция телеметрии в Опенценсус Python

Опенценсус Python соответствует описанным выше спецификациям модели данных `OpenTracing`. Она также поддерживает [контекст трассировки W3C](https://w3c.github.io/trace-context/) , не требуя настройки.

### <a name="incoming-request-correlation"></a>Корреляция входящих запросов

Опенценсус Python сопоставляет заголовки контекста W3C Trace и context из входящих запросов с диапазонами, созданными из самих запросов. Опенценсус сделает это автоматически с интеграцией для этих популярных платформ веб-приложений: Flask, Django и пирамидальная. Необходимо просто заполнить заголовки W3C Trace-контекста [правильным форматом](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) и отправить их вместе с запросом. Вот пример приложения Flask, которое демонстрирует это:

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

Этот код запускает пример приложения Flask на локальном компьютере, прослушиваемый портом `8080`. Для корреляции контекста трассировки вы отправляете запрос в конечную точку. В этом примере можно использовать команду `curl`:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Просмотрев [Формат заголовка Trace-контекст](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), вы можете получить следующие сведения:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Если взглянуть на запись запроса, отправленную на Azure Monitor, можно увидеть поля, заполненные данными заголовка трассировки. Эти данные можно найти в разделе журналы (аналитика) в ресурсе Azure Monitor Application Insights.

![Запросить данные телеметрии в журналах (аналитика)](./media/opencensus-python/0011-correlation.png)

Поле `id` имеет формат `<trace-id>.<span-id>`, где `trace-id` берется из заголовка трассировки, который был передан в запросе, а `span-id` — в виде массива 8 байт для этого диапазона.

Поле `operation_ParentId` имеет формат `<trace-id>.<parent-id>`, где `trace-id` и `parent-id` взяты из заголовка трассировки, переданного в запросе.

### <a name="log-correlation"></a>Корреляция журналов

Опенценсус Python позволяет сопоставлять журналы, добавляя идентификатор трассировки, идентификатор диапазона и флаг выборки в записи журнала. Чтобы добавить эти атрибуты, установите [интеграцию с ведением журнала](https://pypi.org/project/opencensus-ext-logging/)опенценсус. Следующие атрибуты будут добавлены в объекты `LogRecord` Python: `traceId`, `spanId`и `traceSampled`. Обратите внимание, что это вступает в силу только для средств ведения журнала, созданных после интеграции.

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
При выполнении этого кода на консоли выводятся следующие печатные строки:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Обратите внимание, что существует `spanId` для сообщения журнала, находящихся в пределах диапазона. Это тот же `spanId`, который принадлежит диапазону с именем `hello`.

Данные журнала можно экспортировать с помощью `AzureLogHandler`. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Корреляция данных телеметрии в .NET

Со временем .NET определил несколько способов корреляции журналов телеметрии и диагностики:

- `System.Diagnostics.CorrelationManager` позволяет отслеживать [LogicalOperationStack и ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` и трассировка событий Windows(ETW) определяет метод [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` использует [области журнала](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) и HTTP подключают распространение "текущего" контекста.

Но эти методы не включают автоматическую поддержку распределенной трассировки. `DiagnosticSource` поддерживает автоматическую корреляцию между компьютерами. Библиотеки .NET поддерживают `DiagnosticSource` и разрешают автоматическое распространение контекста корреляции между компьютерами через транспорт, например HTTP.

В `DiagnosticSource` [Пошаговые](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) инструкции по отслеживанию действий см. в разделе "действия пользователя".

ASP.NET Core 2,0 поддерживает извлечение заголовков HTTP и запуск новых действий.

`System.Net.Http.HttpClient`, начиная с версии 4.1.0, поддерживает автоматическое внедрение заголовков HTTP корреляции и отслеживание вызовов HTTP в качестве действий.

Для классической ASP.NET существует новый модуль HTTP ( [Microsoft. AspNet. телеметрикоррелатион](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)). Этот модуль реализует корреляцию данных телеметрии с помощью `DiagnosticSource`. Он запускает действие, исходя из заголовков входящего запроса. Он также сопоставляет данные телеметрии от различных стадий обработки запросов, даже если каждый этап обработки службы IIS (IIS) выполняется в другом управляемом потоке.

Пакет SDK для Application Insights, начиная с версии 2.4.0-beta1, использует `DiagnosticSource` и `Activity` для сбора данных телеметрии и их связывания с текущим действием.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Корреляция данных телеметрии в пакете SDK для Java

[Application Insights SDK для Java](../../azure-monitor/app/java-get-started.md) версии 2.0.0 или более поздней поддерживает автоматическую корреляцию телеметрии. Он автоматически заполняет `operation_id` для всех данных телеметрии (таких как трассировки, исключения и пользовательские события), выдаваемых в области запроса. Он также распространяет заголовки корреляции (описанные выше) для вызовов между службами по протоколу HTTP, если настроен [Агент пакета SDK для Java](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Для функции корреляции поддерживаются только вызовы, выполненные через Apache HttpClient. И пружинные RestTemplate, и Феигн можно использовать вместе с Apache HttpClient.

В настоящее время автоматическое распространение контекста между технологиями обмена сообщениями (например, Kafka, RabbitMQ и служебная шина Azure) не поддерживается. Такие сценарии можно кодировать вручную с помощью методов `trackDependency` и `trackRequest`. В этих методах данные телеметрии зависимостей представляют собой сообщение, поставленное производителем. Запрос представляет сообщение, обрабатываемое потребителем. В этом случае и `operation_id`, и `operation_parentId` должны быть указаны в свойствах сообщений.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Корреляция телеметрии в асинхронных приложениях Java

Сведения о том, как сопоставить данные телеметрии в приложении асинхронной загрузки пружины, см. в разделе [Распределенная трассировка в асинхронных приложениях Java](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). В этой статье приводятся рекомендации по инструментированию [среадпултаскексекутор](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) и [среадпултасксчедулер](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)весны.


<a name="java-role-name"></a>
## <a name="role-name"></a>Имя роли

Может потребоваться настроить способ отображения имен компонентов в [схеме приложения](../../azure-monitor/app/app-map.md). Для этого можно вручную задать `cloud_RoleName`, выполнив одно из следующих действий.

- С помощью Application Insights пакета SDK для Java 2.5.0 и более поздних версий можно указать `cloud_RoleName`, добавив `<RoleName>` в файл ApplicationInsights. XML:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Если вы используете пружинную загрузку с Application Insights пружинной загрузкой, необходимо просто задать пользовательское имя для приложения в файле Application. Properties:

  `spring.application.name=<name-of-app>`

  Начальная загрузочная загрузка автоматически назначает `cloudRoleName` значению, введенному для свойства `spring.application.name`.

## <a name="next-steps"></a>Дальнейшие действия

- Напишите [пользовательскую телеметрию](../../azure-monitor/app/api-custom-events-metrics.md).
- Дополнительные сценарии корреляции в ASP.NET Core и ASP.NET см. в разделе [Track Custom Operations](custom-operations-tracking.md).
- Узнайте об [установке свойства cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) для других пакетов SDK.
- Подключите все компоненты своей микрослужбы с помощью Application Insights. Ознакомьтесь со сведениями о [поддерживаемых платформах](../../azure-monitor/app/platforms.md).
- В [этой статье](../../azure-monitor/app/data-model.md) представлена модель данных для Application Insights.
- Узнайте, как [расширять и фильтровать данные телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
- Просмотрите [справочник по конфигурации в Application Insights](configuration-with-applicationinsights-config.md).
