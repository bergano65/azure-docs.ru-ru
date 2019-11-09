---
title: Фильтрация и предварительная обработка в пакете SDK для Azure Application Insights | Документация Майкрософт
description: Из этой статьи вы узнаете, как создать обработчики и инициализаторы телеметрии для того, чтобы пакет SDK мог выполнять фильтрацию, и как добавлять свойства к данным перед отправкой данных телеметрии на портал Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: 550ac9ff3b425e682fdda16501613aa41a80d765
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847249"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights

Вы можете написать и настроить подключаемые модули для пакета SDK для Application Insights, чтобы настроить способ дополнения и обработки телеметрии перед отправкой в службу Application Insights.

* [Выборка](sampling.md) сокращает объем данных телеметрии, не искажая статистические данные. Благодаря выборке связанные точки данных хранятся вместе, что облегчает навигацию между ними во время диагностики проблемы. На портале общее количество умножается, чтобы компенсировать выборку.
* Фильтрация с помощью обработчиков телеметрии позволяет отфильтровывать данные телеметрии в пакете SDK перед их отправкой на сервер. Например, можно уменьшить объем данных телеметрии, исключив запросы от роботов. Фильтрация — это более простой подход к сокращению трафика по сравнению с выборкой. Хотя фильтрация обеспечивает более жесткий контроль над передаваемыми данными, следует не забывать и о влиянии на статистику (например, когда отфильтровываются все успешные запросы).
* [Инициализаторы телеметрии добавляют или изменяют свойства](#add-properties) к любым данным телеметрии, отправляемым из приложения, включая данные телеметрии из стандартных модулей. Например, можно добавить вычисляемые значения или номера версий, по которым будут отфильтрованы данные на портале.
* [API пакета SDK](../../azure-monitor/app/api-custom-events-metrics.md) используется для отправки пользовательских событий и показателей.

Перед началом работы:

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

**Другой способ** — инициализировать фильтр в коде. В подходящем классе инициализации — например, AppStart в `Global.asax.cs` — вставьте свой процессор в цепочку:

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
> Добавление процессора с помощью `ApplicationInsights.config` или `TelemetryConfiguration.Active` недопустимо для ASP.NET Core приложений или при использовании пакета SDK Microsoft. ApplicationInsights. Воркерсервице.

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

Если задан инициализатор телеметрии, он вызывается всякий раз, когда вызывается любой метод Track*(). К ним относятся методы `Track()`, вызываемые стандартными модулями телеметрии. Обычно эти модули не задают свойство, которое уже задал инициализатор. Инициализаторы телеметрии вызываются перед вызовом обработчиков данных телеметрии. Поэтому все расширения, реализованные инициализаторами, видимы для процессоров.

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
> Добавление инициализатора с помощью `ApplicationInsights.config` или `TelemetryConfiguration.Active` недопустимо для ASP.NET Core приложений или при использовании пакета SDK Microsoft. ApplicationInsights. Воркерсервице.

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

## <a name="next"></a>Дальнейшие действия
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md)
* [Выборка](../../azure-monitor/app/sampling.md)
* [Устранение неполадок](../../azure-monitor/app/troubleshoot-faq.md)
