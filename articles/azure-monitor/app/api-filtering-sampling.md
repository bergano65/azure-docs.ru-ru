---
title: Фильтрация и предварительная обработка в пакете SDK для Azure Application Insights | Документация Майкрософт
description: Из этой статьи вы узнаете, как создать обработчики и инициализаторы телеметрии для того, чтобы пакет SDK мог выполнять фильтрацию, и как добавлять свойства к данным перед отправкой данных телеметрии на портал Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 9f4df83ed60ba94913702b9a32a298f0ac62f9f4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666468"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights

Вы можете написать и настроить подключаемые модули для пакета SDK для Application Insights, чтобы настроить способ дополнения и обработки телеметрии перед отправкой в службу Application Insights.

* [Выборка](sampling.md) сокращает объем данных телеметрии, не искажая статистические данные. Благодаря выборке связанные точки данных хранятся вместе, что облегчает навигацию между ними во время диагностики проблемы. На портале общее количество умножается, чтобы компенсировать выборку.
* Фильтрация с помощью обработчиков телеметрии позволяет отфильтровывать данные телеметрии в пакете SDK перед их отправкой на сервер. Например, можно уменьшить объем данных телеметрии, исключив запросы от роботов. Фильтрация — это более простой подход к сокращению трафика по сравнению с выборкой. Хотя фильтрация обеспечивает более жесткий контроль над передаваемыми данными, следует не забывать и о влиянии на статистику (например, когда отфильтровываются все успешные запросы).
* [Инициализаторы телеметрии добавляют или изменяют свойства](#add-properties) к любым данным телеметрии, отправляемым из приложения, включая данные телеметрии из стандартных модулей. Например, можно добавить вычисляемые значения или номера версий, по которым будут отфильтрованы данные на портале.
* [API пакета SDK](../../azure-monitor/app/api-custom-events-metrics.md) используется для отправки пользовательских событий и показателей.

Перед началом.

* Установите соответствующий пакет SDK для приложения: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [не HTTP/Worker для .NET, .NET Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) или [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Фильтрация

Этот метод обеспечивает прямое управление тем, что включено или исключено из потока телеметрии. Фильтрацию можно использовать для удаления элементов телеметрии, отправляемых в Application Insights. Этот метод можно использовать совместно с выборкой или по отдельности.

Чтобы отфильтровать телеметрию, необходимо создать обработчик данных телеметрии и зарегистрировать его в `TelemetryConfiguration`. Все данные телеметрии проходят через процессор, и вы можете удалить его из потока или присвоить его следующему процессору в цепочке. Сюда входят данные телеметрии из стандартных модулей, таких как сборщик HTTP-запросов и сборщик зависимостей, а также данные телеметрии, которые вы проведете самостоятельно. Например, можно отфильтровать данные телеметрии о запросах из программ-роботов или успешных вызовах зависимости.

> [!WARNING]
> Фильтрация данных телеметрии, отправленных из пакета SDK с помощью обработчиков, может исказить отображаемую на портале статистику и затруднить отслеживание связанных элементов.
>
> Вместо этого попробуйте использовать [выборку](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Создание обработчика данных телеметрии (C#)

1. Чтобы создать фильтр, реализуйте `ITelemetryProcessor`.

    Обратите внимание, что обработчики данных телеметрии создают цепь обработки. При создании экземпляра обработчика данных телеметрии предоставляется ссылка на следующий процессор в цепочке. Когда точка данных телеметрии передается в метод обработки, она выполняет свою работу, а затем вызывает (или не вызывает) следующий обработчик данных телеметрии в цепочке.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Добавьте процессор.

**Приложения ASP.NET** Вставьте этот фрагмент в файл ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Можно передавать строковые значения из файла конфигурации, указав открытые именованные свойства в классе.

> [!WARNING]
> Будьте внимательны и задайте имя типа и имена свойств в файле конфигурации, совпадающие с именами классов и свойств в коде. Если файл конфигурации ссылается на несуществующий тип или свойство, пакет SDK может не суметь отправить данные телеметрии без уведомления.
>

**Другой способ** — инициализировать фильтр в коде. В подходящем классе инициализации — например, AppStart в `Global.asax.cs` вставьте свой процессор в цепочку:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Клиенты TelemetryClient, созданные после этой точки, будут использовать обработчики.

**Приложения ASP.NET Core и рабочих служб**

> [!NOTE]
> Добавление процессора с помощью `ApplicationInsights.config` или использование `TelemetryConfiguration.Active` недопустимо для ASP.NET Core приложений или при использовании пакета SDK Microsoft. ApplicationInsights. Воркерсервице.

Для приложений, написанных с помощью [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) или [воркерсервице](worker-service.md#adding-telemetry-processors), Добавление нового `TelemetryProcessor` выполняется с помощью метода расширения `AddApplicationInsightsTelemetryProcessor` для `IServiceCollection`, как показано ниже. Этот метод вызывается в методе `ConfigureServices` класса `Startup.cs`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Примеры фильтров

#### <a name="synthetic-requests"></a>Искусственные запросы

Вы можете отфильтровывать программы-роботы и веб-тесты. Хотя обозреватель метрик предоставляет возможность фильтрации искусственных источников, этот параметр сокращает объем трафика и размер приема путем фильтрации в самом пакете SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Сбой проверки подлинности

Отфильтруйте запросы с ответом 401.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Фильтрация быстрых удаленных вызовов зависимостей

Если требуется диагностировать только вызовы, которые выполняются медленно, можно отфильтровать быстрые вызовы.

> [!NOTE]
> Это приведет к искажению статистических данных, отображаемых на портале.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Неполадки диагностики зависимостей

[этом блоге](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) описан проект, в котором диагностика неполадок с зависимостями реализована в виде автоматического опрашивания зависимостей.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Веб-приложения JavaScript

**Фильтрация с помощью ITelemetryInitializer**

1. Создайте функцию обратного вызова инициализатора телеметрии. Функция обратного вызова принимает `ITelemetryItem` в качестве параметра, который обрабатывают события. Возврат `false` из этого обратного вызова приводит к исключению элемента телеметрии.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Добавьте обратный вызов инициализатора телеметрии:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Добавить или изменить свойства: ITelemetryInitializer


Используйте инициализаторы телеметрии для обогащения телеметрии с дополнительной информацией и (или) для переопределения свойств телеметрии, заданных стандартными модулями телеметрии.

Например, Application Insights для сбора данных телеметрии о HTTP-запросах. По умолчанию любой запрос с кодом ответа > = 400 он помечает как неудавшийся. Если вам нужно, чтобы значение 400 считалось успешным, задайте инициализатор телеметрии, в котором можно настроить свойство Success.

Если задан инициализатор телеметрии, он вызывается всякий раз, когда вызывается любой метод Track*(). К ним относятся `Track()` методы, вызываемые стандартными модулями телеметрии. Обычно эти модули не задают свойство, которое уже задал инициализатор. Инициализаторы телеметрии вызываются перед вызовом обработчиков данных телеметрии. Поэтому все расширения, реализованные инициализаторами, видимы для процессоров.

**Определение инициализатора**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**Приложения ASP.NET: Загрузка инициализатора**

В ApplicationInsights.config.:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Другой способ* — создать экземпляр инициализатора в коде, например в Global.aspx.cs.

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Дополнительную информацию см. здесь.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core и приложения рабочей службы: Загрузите инициализатор**

> [!NOTE]
> Добавление инициализатора с помощью `ApplicationInsights.config` или с помощью `TelemetryConfiguration.Active` недопустимо для ASP.NET Core приложений или при использовании пакета SDK Microsoft. ApplicationInsights. Воркерсервице.

Для приложений, написанных с помощью [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) или [воркерсервице](worker-service.md#adding-telemetryinitializers), Добавление нового `TelemetryInitializer` выполняется путем добавления его в контейнер внедрения зависимостей, как показано ниже. Это делается в методе `Startup.ConfigureServices`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Инициализаторы телеметрии Java

[Документация SDK для Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Затем зарегистрируйте пользовательский инициализатор в файле applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Инициализаторы телеметрии JavaScript
*JavaScript*

Вставьте инициализатор телеметрии сразу после кода инициализации, полученного на портале:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Краткое описание ненастраиваемых свойств, доступных в коллекции telemetryItem, см. в разделе [Экспорт модели данных Application Insights](../../azure-monitor/app/export-data-model.md).

Можно добавить столько инициализаторов, сколько нужно, и они вызываются в порядке их добавления.

### <a name="opencensus-python-telemetry-processors"></a>Обработчики телеметрии Опенценсус Python

Обработчики данных телеметрии в Опенценсус Python — это просто функции обратного вызова, которые вызываются для обработки телеметрии перед их экспортом. Функция обратного вызова должна принимать тип данных [конверта](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) в качестве параметра. Чтобы отфильтровать данные телеметрии из экспорта, убедитесь, что функция обратного вызова возвращает `False`. Схему для Azure Monitor типов данных можно увидеть на конвертах [здесь](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Можно добавить столько процессоров, сколько требуется, и они вызываются в порядке их добавления. Если один процессор должен вызывать исключение, он не влияет на следующие процессоры.

### <a name="example-telemetryinitializers"></a>Пример Telemetryinitializer

#### <a name="add-custom-property"></a>Добавить пользовательское свойство

Следующий пример инициализатора добавляет пользовательское свойство в каждую отслеживание телеметрии.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Добавить имя роли облака

Следующий пример инициализатора задает имя облачной роли для каждой отслеживанию телеметрии.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor и ITelemetryInitializer

Различия между обработчиком и инициализатором данных телеметрии

* Существует несколько пересечений, которые можно выполнять с ними: обе можно использовать для добавления или изменения свойств телеметрии, хотя для этой цели рекомендуется использовать инициализаторы.
* Свойство TelemetryInitializers всегда выполняется перед TelemetryProcessors.
* Telemetryinitializer может вызываться более одного раза. По соглашению они не устанавливают ни одно свойство, которое уже было задано.
* Свойство TelemetryProcessors позволяет полностью заменить или удалить элемент телеметрии.
* Все зарегистрированные Telemetryinitializer гарантированно вызываются для каждого элемента телеметрии. Для обработчиков телеметрии пакет SDK гарантирует вызов очень первого обработчика данных телеметрии. Независимо от того, вызываются ли остальные процессоры или нет, решение принимается предыдущими процессорами телеметрии.
* Используйте Telemetryinitializer для обогащения телеметрии с дополнительными свойствами или переопределите существующую. Используйте Телеметрипроцессор для фильтрации данных телеметрии.

## <a name="troubleshooting-applicationinsightsconfig"></a>Устранение неполадок с файлом ApplicationInsights.config

* Убедитесь, что полное имя типа и имя сборки указаны правильно.
* Убедитесь, что файл applicationinsights.config находится в выходном каталоге и содержит все последние изменения.

## <a name="reference-docs"></a>Справочная документация

* [Обзор API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Код пакета SDK

* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [Пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Следующие шаги
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md)
* [Выборка](../../azure-monitor/app/sampling.md)
* [Устранение неполадок](../../azure-monitor/app/troubleshoot-faq.md)
