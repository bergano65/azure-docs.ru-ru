---
title: Выборка данных телеметрии в Azure Application Insights | Документация Майкрософт
description: Как управлять объемом данных телеметрии.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255487"
---
# <a name="sampling-in-application-insights"></a>Выборка в Application Insights

Выборка — это функция [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md), Это рекомендуемый способ снижения телеметрического трафика, затрат на передачу данных и затрат на хранение, сохраняя при этом статистически правильный анализ данных приложения. Выборка также помогает избежать обработки информации о приложениях, задушив телеметрию. Фильтр выборки выбирает элементы, которые связаны между собой, так что вы можете перемещаться между элементами, когда вы делаете диагностические исследования.

Когда метрические подсчеты представлены на портале, они ренормализируются с учетом выборки. Это сводит к минимуму любое влияние на статистику.

## <a name="brief-summary"></a>Краткое резюме

* Существует три различных типа выборки: адаптивная выборка, выборка с фиксированной ставкой и выборка при углашения.
* Адаптивная выборка включена по умолчанию во всех последних версиях ASP.NET приложений и ASP.NET основных комплектов разработки программного обеспечения (SDK). Он также используется [Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-overview)
* Выборка с фиксированной ставкой доступна в последних версиях SDK Application Insights для ASP.NET, ASP.NET Core, Java (как агента, так и SDK) и Python.
* Выборка приема работает на конечную точку службы Application Insights. Он применяется только тогда, когда никакой другой выборки не действует. Если SDK пробует телеметрию, выборка приема отключается.
* Для веб-приложений, если вы регистрируете пользовательские события и должны убедиться, что набор `OperationId` событий сохраняется или отбрасывается вместе, события должны иметь одинаковое значение.
* При написании запросов аналитики необходимо [учитывать выборку](../../azure-monitor/log-query/aggregations.md). В частности, вместо простого подсчета записей следует использовать функцию `summarize sum(itemCount)`.
* Некоторые типы телеметрии, включая показатели производительности и пользовательские метрики, всегда сохраняются независимо от того, включена выборка или нет.

В следующей таблице кратко излагаются типы выборки, доступные для каждого SDK и тип применения:

| Пакет SDK для Application Insights | Адаптивная выборка поддерживается | Выборка с фиксированной ставкой поддерживается | Выборка приема поддерживается |
|-|-|-|-|
| ASP.NET | [Да (по умолчанию)](#configuring-adaptive-sampling-for-aspnet-applications) | [Да](#configuring-fixed-rate-sampling-for-aspnet-applications) | Только в том случае, если не действует какая-либо другая выборка |
| ASP.NET Core | [Да (по умолчанию)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Да](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Только в том случае, если не действует какая-либо другая выборка |
| Функции Azure | [Да (по умолчанию)](#configuring-adaptive-sampling-for-azure-functions) | нет | Только в том случае, если не действует какая-либо другая выборка |
| Java | нет | [Да](#configuring-fixed-rate-sampling-for-java-applications) | Только в том случае, если не действует какая-либо другая выборка |
| Python | нет | [Да](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Только в том случае, если не действует какая-либо другая выборка |
| Все остальные | нет | нет | [Да](#ingestion-sampling) |

> [!NOTE]
> Информация на большей части этой страницы относится к текущим версиям SDK Application Insights. Для получения информации о старых версиях SDK, [см.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Типы выборки

Существует три различных метода выборки:

* **Адаптивная выборка** автоматически корректирует объем телеметрии, отправленной из SDK в приложении ASP.NET/ASP.NET Core, а также с Azure Functions. Это выборка по умолчанию при использовании ASP.NET или ASP.NET Core SDK. Адаптивная выборка в настоящее время доступна только для телеметрии ASP.NET сервера и для функций Azure.

* **Выборка с фиксированной ставкой** уменьшает объем телеметрии, отправляемых как с ASP.NET или ASP.NET сервера Core или Java, так и из браузеров пользователей. Частоту вы задаете сами. Благодаря тому, что клиент и сервер будут синхронизировать свои выборки, при поиске вы сможете перемещаться между связанными представлениями страниц и запросами.

* **Выборка приема** происходит в конечном пункте службы Application Insights. отклоняет некоторые данные телеметрии, поступающие из приложения, с заданной скоростью выборки. Выборка не сокращает трафик телеметрии, отправляемый из вашего приложения, но помогает оставаться в пределах месячной квоты. Основным преимуществом выборки является то, что вы можете установить скорость выборки без передислокации приложения. Выборка приема работает равномерно для всех серверов и клиентов, но она не применяется при эксплуатации любых других типов выборки.

> [!IMPORTANT]
> При работе адаптивных или методик проб выборки с фиксированной скоростью выборка отключается.

## <a name="adaptive-sampling"></a>Адаптивная выборка

Адаптивная выборка влияет на объем данных телеметрии, отправляемых в конечную точку службы Application Insights из приложения веб-сервера.

> [!TIP]
> Адаптивная выборка включена по умолчанию при использовании ASP.NET SDK или ASP.NET Core SDK, а также включена по умолчанию для функций Azure.

Объем корректируется автоматически, чтобы держать в пределах указанной `MaxTelemetryItemsPerSecond`максимальной скорости трафика, и контролируется через настройки. Если приложение производит небольшое количество телеметрии, например, при отладке или из-за низкого использования, элементы не будут удалены процессором выборки до тех пор, пока объем ниже. `MaxTelemetryItemsPerSecond` По мере увеличения объема телеметрии скорость отбора проб корректируется таким образом, чтобы достичь целевого объема. Корректировка пересчитывается через регулярные интервалы времени в зависимости от скользящего среднего для скорости исходящей передачи.

Для получения целевого объема некоторые формируемые данные телеметрии игнорируются. Однако, как и в других типах выборки, алгоритм сохраняет связанные элементы телеметрии. Например, при проверке телеметрии в поиске это позволит найти запрос, связанный с определенным исключением.

Счетчики метрик, например частота запросов и частота исключений, корректируются с учетом частоты выборки, так что в обозревателе метрик отображаются приблизительно точные значения.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Настройка адаптивной выборки для ASP.NET приложений

> [!NOTE]
> Этот раздел распространяется на ASP.NET приложений, а не на ASP.NET основных приложений. [Подробнее о настройке адаптивной выборки для ASP.NET основных приложений можно позже в этом документе.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

В, [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)вы можете настроить несколько параметров в `AdaptiveSamplingTelemetryProcessor` уде. Ниже представлены значения по умолчанию.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Целевая частота, к которой стремится адаптивный алгоритм **на каждом узле сервера**. Если веб-приложение выполняется на множестве узлов, нужно уменьшить это значение, чтобы не превышать целевую скорость трафика на портале Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Интервал, с которым переоценивается текущая скорость телеметрии. Вычисление выполняется на основе скользящего среднего. Вы можете сократить этот интервал, если наблюдаете неожиданные скачки данных телеметрии в сторону увеличения.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    При изменении процентной стоимости выборки, как скоро после этого мы можем снизить процент выборки снова, чтобы захватить меньше данных?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    При изменении процентной стоимости выборки, как скоро после этого мы можем увеличить процент выборки снова, чтобы захватить больше данных?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    По мере изменения процентной доли выборки, какое минимальное значение нам разрешено установить?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    По мере изменения процентного показателя выборки, какое максимальное значение нам разрешено установить?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    При расчете скользящей средней это определяет вес, который должен быть приписан к самому последнему значению. Используйте значение не больше 1. Использование значений ниже рекомендуемых приводит к тому, что скорость реагирования алгоритма на резкие изменения замедляется.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Количество телеметрии для образца, когда приложение только началось. Не уменьшайте это значение во время отладки.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Полуколонновый делимитировайся список типов, которые вы не хотите быть предметом отбора проб. `Dependency`Признанные типы: `Exception`, `Request` `Event` `Trace`, , `PageView`. Вся телеметрия указанных типов передается; типы, которые не указаны, будут отобраны.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Полуколонновый делимитировайся список типов, которые вы хотите, чтобы подлежать выборке. `Dependency`Признанные типы: `Exception`, `Request` `Event` `Trace`, , `PageView`. Указанные типы будут отобраны; все телеметрии других типов всегда будут передаваться.

**Чтобы отключить** адаптивную `AdaptiveSamplingTelemetryProcessor` выборку, удалите узла (ы) из. `ApplicationInsights.config`

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Альтернатива: Настройка адаптивной выборки в коде

Вместо установки параметра `.config` выборки в файле можно запрограммировать установить эти значения.

1. Удалите `AdaptiveSamplingTelemetryProcessor` все узла (ы) из файла. `.config`
2. Используйте следующий фрагмент для настройки адаптивной выборки:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Сведения о процессорах телеметрии](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Вы также можете настроить скорость отбора проб для каждого типа телеметрии по отдельности, или даже может исключить определенные типы из выборки на всех:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Настройка адаптивной выборки для ASP.NET основных приложений

Нет для `ApplicationInsights.config` ASP.NET основных приложений, поэтому вся конфигурация выполняется с помощью кода.
Адаптивная выборка включена для всех приложений ASP.NET Core по умолчанию. Вы можете отключить или настроить поведение выборки.

#### <a name="turning-off-adaptive-sampling"></a>Отключение адаптивной выборки

Функция выборки по умолчанию может быть отключена `ConfigureServices`при `ApplicationInsightsServiceOptions` добавлении службы Application Insights в методе, используя в файле: `Startup.cs`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Вышеупомянутый код отсванет адаптивную выборку. Чтобы добавить выборку с дополнительными параметрами настройки, выполните следующие действия.

#### <a name="configure-sampling-settings"></a>Настройка параметров выборки

Чтобы настроить поведение выборки, используйте методы расширения `TelemetryProcessorChainBuilder`, как показано ниже.

> [!IMPORTANT]
> Если вы используете этот метод для настройки `aiOptions.EnableAdaptiveSampling` выборки, пожалуйста, установить свойство `false` при вызове `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Настройка адаптивной выборки для функций Azure

Следуйте инструкциям [этой страницы](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) для настройки адаптивной выборки для приложений, работающих в Azure Functions.

## <a name="fixed-rate-sampling"></a>Выборка с фиксированной ставкой

Выборка с фиксированной ставкой снижает трафик, отправляемый с веб-сервера и веб-браузеров. В отличие от адаптивной выборки объем данных уменьшается в фиксированном, заданном вами размере. Выборка с фиксированной ставкой доступна для приложений ASP.NET, ASP.NET Core, Java и Python.

Как и другие методы отбора проб, это также сохраняет связанные элементы. Он также синхронизирует выборку клиента и сервера таким образом, чтобы связанные элементы сохранялись - например, когда вы смотрите на представление страницы в Поиске, вы можете найти связанные с ним запросы сервера. 

В обозревателе метрик такие параметры, как счетчики запросов и исключений, умножаются на коэффициент, компенсирующий частоту выборки, что позволяет получать примерно точные значения.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Настройка выборки с фиксированной ставкой для ASP.NET приложений

1. **Отвратительная адаптивная выборка**: В [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), удалить или прокомментировать `AdaptiveSamplingTelemetryProcessor` узла.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Включите модуль выборки с фиксированной частотой.** Добавьте этот фрагмент [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)в:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Кроме того, вместо установки параметра выборки в файле `ApplicationInsights.config` можно запрограммировать установить следующие значения:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Сведения о процессорах телеметрии](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Настройка выборки с фиксированной ставкой для ASP.NET основных приложений

1. **Отключить адаптивную выборку**: `ConfigureServices` Изменения могут `ApplicationInsightsServiceOptions`быть внесены в метод, используя:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Включите модуль выборки с фиксированной частотой.** Изменения могут быть `Configure` внесены в метод, как показано в нижеприведенном фрагменте:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Настройка выборки с фиксированной ставкой для Java-приложений

По умолчанию выборка не включена в Java-агенте и SDK. В настоящее время он поддерживает только выборку фиксированной ставки. Адаптивная выборка не поддерживается в Java.

#### <a name="configuring-java-agent"></a>Настройка Java-агента

1. Скачать [applicationinsights-агент-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. Для включения выборки `ApplicationInsights.json` добавьте в файл следующее:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Настройка Java SDK

1. Скачать и настроить веб-приложение с помощью [новейших приложений Insights Java SDK.](../../azure-monitor/app/java-get-started.md)

2. **Включите модуль выборки с фиксированной** `ApplicationInsights.xml` ставкой, добавив следующий фрагмент для файла:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Вы можете включить или исключить определенные типы телеметрии `Processor` из `FixedRateSamplingTelemetryProcessor`выборки, используя следующие теги внутри тега:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Типы телеметрии, которые могут быть `Dependency`включены или исключены из выборки: `Event`, , `Exception` `PageView` `Request`, и `Trace`.

> [!NOTE]
> В качестве процента выборки выберите значение в процентах, близкое к 100/N, где N — это целое число.  В настоящее время выборка не поддерживает другие значения.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Настройка выборки с фиксированной ставкой для приложений OpenCensus Python

Инструмент приложения с последними [OpenCensus Azure Monitor экспортеров](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Выборка с фиксированной ставкой недоступна для экспортера метрик. Это означает, что пользовательские метрики являются единственными типами телеметрии, где выборка не может быть настроена. Экспортер метрик будет отправлять всю телеметрию, которую он отслеживает.

#### <a name="fixed-rate-sampling-for-tracing"></a>Выборка с фиксированной ставкой для трассировки ####
Вы можете указать `sampler` как часть конфигурации `Tracer`. Если не предусмотрен явный `ProbabilitySampler` сэмплер, он будет использоваться по умолчанию. По `ProbabilitySampler` умолчанию будет использоваться ставка 1/10000, то есть один из каждых 10000 запросов будет отправлен в Application Insights. Чтобы указать свою частоту выборки, см. ниже.

Чтобы указать частоту выборки, убедитесь, что ваш `Tracer` определяет пробоотборщика со скоростью отбора проб между 0,0 и 1,0 включительно. Коэффициент выборки 1,0 составляет 100%, что означает, что все ваши запросы будут отправлены в качестве телеметрии в Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Выборка с фиксированной ставкой для журналов ####
Можно настроить выборку с `AzureLogHandler` фиксированной ставкой, изменив факультативный `logging_sampling_rate` аргумент. Если аргумент не поставляется, будет использоваться коэффициент выборки 1,0. Коэффициент выборки 1,0 составляет 100%, что означает, что все ваши запросы будут отправлены в качестве телеметрии в Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Настройка выборки с фиксированной ставкой для веб-страниц с помощью JavaScript

Веб-страницы на основе JavaScript могут быть настроены для использования приложений Insights. Телеметрия отправляется из клиентского приложения, работающегося в браузере пользователя, и страницы могут быть размещены с любого сервера.

При [настройке веб-страниц на основе JavaScript для Application Insights](javascript.md)измените фрагмент JavaScript, который вы получаете с портала Application Insights.

> [!TIP]
> В ASP.NET приложений с включенным JavaScript `_Layout.cshtml`фрагмент обычно входит в .

Вставьте строку, похожую на `samplingPercentage: 10,`, перед ключом инструментирования:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

В качестве процента выборки выберите значение в процентах, близкое к 100/N, где N — это целое число. В настоящее время выборка не поддерживает другие значения.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Координация выборки на стороне сервера и клиента

Клиентская сторона JavaScript SDK участвует в выборке с фиксированной ставкой в сочетании с серверной стороной SDK. Инструментальные страницы будут отправлять телеметрию на стороне клиента только от того же пользователя, для которого серверная сторона SDK приняла решение включить в выборку. Эта логика предназначена для поддержания целостности пользовательских сеансов в приложениях на стороне клиента и сервера. В результате, из какого-либо конкретного элемента телеметрии в Application Insights вы можете найти все другие элементы телеметрии для этого пользователя или сеанса и в поиске, вы можете перемещаться между соответствующими представлениями страниц и запросами.

Если телеметрия клиента и сервера не показывает скоординированных образцов:

* Убедитесь, что вы включили выборку как на сервере, так и в клиенте.
* Проверьте, указан ли процент выборки на клиенте и сервере.
* Убедитесь в том, что используется пакет SDK версии 2.0 или выше.

## <a name="ingestion-sampling"></a>Выборка приема

Выборка приема данных работает в точке, где телеметрия с вашего веб-сервера, браузеров и устройств достигает точки обслуживания Application Insights. Несмотря на то, что трафик телеметрии, отправляемой из приложения, такая выборка не уменьшает, она сокращает объем данных для обработки и сохранения в службе Application Insights, а значит и размер оплаты.

Используйте этот тип выборки, если ваше приложение часто выходит за рамки ежемесячной квоты, а возможности использовать один из типов выборки на основе SDK нет. 

Задайте частоту выборки на странице "Usage and estimated costs" (Данные об использовании и предполагаемые расходы).

![Из лезвия обзора приложения нажмите «Настройки», квоты, образцы, затем выберите частоту выборки и нажмите «Обновление».](./media/sampling/data-sampling.png)

Как и в других типах выборки, алгоритм сохраняет связанные элементы телеметрии. Например, при проверке телеметрии в поиске это позволит найти запрос, связанный с определенным исключением. Правильно сохраняются счетчики метрик, например частота запросов и частота исключений.

Точки данных, отклоненные выборкой, доступны не во всех функциях Application Insights, например [Непрерывный экспорт](../../azure-monitor/app/export-telemetry.md).

Выборка заглажения не работает во время работы адаптивной или фиксированной выборки. Адаптивная выборка включена по умолчанию при использовании ASP.NET SDK или ASP.NET Core SDK, или когда Application Insights включена в [службе приложений Azure](azure-web-apps.md) или с помощью Status Monitor. Когда телеметрия получена в конечном пункте службы Application Insights, она исследует телеметрию, и если сообщается, что частота выборки составляет менее 100% (что указывает на то, что телеметрия проходит отбор проб), то уровень пробоотбора, который вы установили, игнорируется.

> [!WARNING]
> Значение, указанное на плитке портала, указывает значение, которое устанавливается для выборки при проглатываниях. Он не отражает фактическую частоту выборки, если какой-либо выборки SDK (адаптивная или фиксированная ставка выборки) находится в эксплуатации.

## <a name="when-to-use-sampling"></a>Когда использовать выборку

В целом, для большинства малых и средних приложений вам не нужна выборка. Наиболее полезные диагностические сведения и наиболее точную статистику можно получить, собирая данные обо всех действиях пользователей. 

Основные преимущества выборки:

* Служба Application Insights отбрасывает ("дроссесолы") точки данных, когда ваше приложение отправляет очень высокую скорость телеметрии в короткий промежуток времени. Выборка снижает вероятность того, что ваше приложение увидит происходит ьсядя.
* Вы остаетесь в пределах [квоты](pricing.md) на точки данных для своей ценовой категории. 
* Вам нужно сократить сетевой трафик, который тратится на сбор данных телеметрии. 

### <a name="which-type-of-sampling-should-i-use"></a>Какой тип выборки следует использовать?

**Используйте выборку приема, если:**

* Вы часто используете свою ежемесячную квоту телеметрии.
* Вы получаете слишком много телеметрии из веб-браузеров ваших пользователей.
* Вы используете версию пакета SDK, которая не поддерживает выборки, например версию пакета для ASP.NET ниже 2.

**Используйте выборку с фиксированной частотой, если:**

* Требуется синхронизированная выборка между клиентом и сервером, чтобы при расследовании событий в [Поиске](../../azure-monitor/app/diagnostic-search.md)можно было перемещаться между связанными событиями на клиенте и сервере, такими как просмотры страниц и запросы HTTP.
* Вам нужна уверенность в выборе соответствующего процента выборки для вашего приложения. Он должен быть достаточно высоким, чтобы получить точные метрики и при этом не превышать ценовую квоту и ограничения регулирования.

**Используйте адаптивную выборку:**

Если условия для использования других форм выборки не применяются, мы рекомендуем адаптивную выборку. Эта настройка включена по умолчанию в ASP.NET/ASP.NET Core SDK. Это не приведет к сокращению трафика до тех пор, пока не будет достигнута определенная минимальная ставка, поэтому низкоиспользоваться сайты, вероятно, не будут отобраны вообще.

## <a name="knowing-whether-sampling-is-in-operation"></a>Зная, работает ли выборка

Чтобы узнать фактическую частоту выборки (где бы она ни применялась), выполните такой [запрос аналитики](../../azure-monitor/app/analytics.md) :

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Если вы `RetainedPercentage` видите, что для любого типа меньше, чем 100, то этот тип телеметрии в настоящее время выборка.

> [!IMPORTANT]
> Application Insights не является образцом сеанса, метрик (включая пользовательские метрики) или типов телеметрии производительности в любом из методов выборки. Эти типы всегда исключаются из выборки, поскольку снижение точности может быть крайне нежелательным для этих типов телеметрии.

## <a name="how-sampling-works"></a>Как работает выборка

Алгоритм выборки определяет, какие элементы телеметрии следует упасть, а какие сохранить. Это верно независимо от того, выполняется ли выборка SDK или в службе Application Insights. Решение выборки основано на нескольких правилах, цель которых — сохранение всех точек взаимосвязанных данных без изменений, а также реализация функции диагностики в Application Insights даже с использованием сокращенного набора данных. Например, если в ашего приложении не удался запрос, включенный в пример, дополнительные элементы телеметрии (такие как исключение и следы, зарегистрированные для этого запроса) будут сохранены. Выборка либо сохраняет, либо сбрасывает их все вместе. Поэтому при просмотре сведений о запросе в Application Insights вы всегда будете видеть запрос вместе со связанными элементами телеметрии.

Решение о выборке основано на идентификаторе операции запроса, что означает, что все элементы телеметрии, принадлежащие к конкретной операции, либо сохраняются, либо отброшены. Для элементов телеметрии, которые не имеют набора идентификаторов операций (например, элементы телеметрии, сообщаемые из асинхронных потоков без контекста HTTP), выборка просто фиксирует процент элементов телеметрии каждого типа.

Представляя вам данные телеметрии, служба Application Insights корректирует метрики в соответствии с процентом выборки, который использовался во время сбора. Это делается, чтобы компенсировать отсутствующие точки данных. Следовательно, при просмотре данных телеметрии в Application Insights пользователи видят статистически правильные приблизительные значения, очень близкие к реальным цифрам.

Точность приблизительного значения в значительной степени зависит от заданного процента выборки. Кроме того, точность возрастает для приложений, обрабатывающих большой объем сходных запросов от большого количества пользователей. С другой стороны, для приложений, которые не работают с существенной нагрузкой, выборка не требуется, так как такие приложения обычно могут отправлять все данные телеметрии, не выходя за пределы квоты и не вызывая потерю данных в результате регулирования. 

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

*Каково поведение выборки по умолчанию в ASP.NET и ASP.NET основных SDK?*

* Если вы используете одну из последних версий вышеуказанного SDK, адаптивная выборка включена по умолчанию с пятью элементами телеметрии в секунду.
  По умолчанию добавлены два `AdaptiveSamplingTelemetryProcessor` узла, `Event` один из них включает тип `Event` в выборке, в то время как другой исключает тип из выборки. Эта конфигурация означает, что SDK будет пытаться ограничить элементы `Event` телеметрии пятью элементами телеметрии типов `Events` и пятью элементами телеметрии всех других типов вместе взятых, тем самым гарантируя, что выборка будет взята отдельно от других типов телеметрии. События обычно используются для бизнес-телеметрии, и, скорее всего, не должны зависеть от диагностических объемов телеметрии.
  
  Ниже отображается `ApplicationInsights.config` генерируемый файл по умолчанию. В ASP.NET Core в коде включено то же поведение по умолчанию. Используйте [примеры в предыдущем разделе этой страницы,](#configuring-adaptive-sampling-for-aspnet-core-applications) чтобы изменить это поведение по умолчанию.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Можно ли пробовать телеметрию более одного раза?*

* Нет. ВыборкаTelemetryProcessors игнорирует элементы из соображений выборки, если товар уже отобран. То же самое относится и к выборке, которая не будет применяться к тем элементам, которые уже отобраны в самом SDK.

*Почему выборка — это не простой сбор X процентов данных телеметрии каждого типа?*

* Хотя такой подход к выборке обеспечит высокий уровень точности приближений метрики, он нарушит способность соотносить диагностические данные на пользователя, сеанс и запрос, что имеет решающее значение для диагностики. Таким образом, выборка лучше работает с политиками, такими как "собрать все элементы телеметрии для X процентов пользователей приложений" или "собрать всю телеметрию для X процентов запросов приложений". Для элементов телеметрии, не связанных с запросами (например, фоновая асинхронная обработка), резервное копирование заключается в том, чтобы "собрать X процент всех элементов для каждого типа телеметрии". 

*Может ли процент выборки меняться со временем?*

* Да, адаптивная выборка постепенно меняет процент выборки в зависимости от текущего объема данных телеметрии.

*Если я использую выборку с фиксированной частотой, как узнать, какой процент выборки будет оптимальным для моего приложения?*

* Один из способов — начать с адаптивной выборки, узнать подобранную частоту (см. вопрос выше) и переключиться на выборку с этой фиксированной частотой. 
  
    В противном случае придется только угадывать. Анализируйте текущее использование телеметрии в Application Insights, наблюдайте осуществляемое регулирование и оценивайте объем собранных данных телеметрии. Эти три фактора в сочетании с выбранной ценовой категорией позволяют оценить объем собранных данных телеметрии, который может потребоваться сократить. Однако увеличение числа пользователей или некоторые другие факторы, меняющие объем данных телеметрии, могут сделать оценку недействительной.

*Что произойдет, если я назначу процент выборки, чтобы быть слишком низким?*

* Чрезмерно низкие проценты выборки вызывают чрезмерно агрессивную выборку и снижают точность приближений, когда Application Insights пытается компенсировать визуализацию данных для уменьшения объема данных. Кроме того, ваш диагностический опыт может быть негативное влияние, так как некоторые из редко отказа или медленные запросы могут быть отобраны.

*Что произойдет, если я назначу процент выборки, чтобы быть слишком высоким?*

* Слишком высокая процентная настройка выборки (недостаточно агрессивная) приводит к недостаточному сокращению объема собранной телеметрии. У вас по-прежнему может наблюдаться потеря данных телеметрии, связанная с регулированием, а затраты на использование Application Insights могут быть выше запланированных из-за избыточных расходов.

*На каких платформах можно использовать выборку?*

* Выборка приема может выполняться автоматически, когда объем данных телеметрии превышает определенный порог, если пакет SDK не выполняет выборку. Эта конфигурация будет работать, например, если вы используете старую версию ASP.NET SDK или Java SDK.
* Если вы используете текущую ASP.NET или ASP.NET Core SDK (размещены либо в Azure, либо на собственном сервере), вы получаете адаптивную выборку по умолчанию, но вы можете переключиться на фиксированную ставку, как описано выше. При выборке с фиксированной частотой пакет SDK браузера выполняет автоматическую синхронизацию с событиями, связанными с выборкой. 
* Если вы используете текущий агент Java, `ApplicationInsights.json` можно настроить (для Java `ApplicationInsights.xml`SDK, настроить) для включанизании выборки с фиксированной ставкой. По умолчанию выборка отключена. При выборке с фиксированной ставкой браузер SDK и сервер автоматически синхронизируются с образцами связанных событий.

*Есть определенные редкие события, которые я всегда хочу просматривать. Как сделать так, чтобы модуль выборки не отфильтровывал их?*

* Лучший способ достичь этого заключается в том, чтобы написать пользовательский [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), который устанавливает `SamplingPercentage` до 100 на телеметрии пункт вы хотите сохранить, как показано ниже. Поскольку инициализаторы гарантированно будут запускаться до процессоров телеметрии (включая выборку), это гарантирует, что все методы отбора проб будут игнорировать этот пункт из любых соображений выборки. Пользовательские инициалии телеметрии доступны в ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK и Java SDK. Например, можно настроить инициатор телеметрии с помощью ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Старые версии SDK

Адаптивная выборка доступна для Приложения Исследования SDK для ASP.NET v2.0.0-beta3 и позже, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 и позже, и включен по умолчанию.

Выборка с фиксированной ставкой является особенностью SDK в ASP.NET версиях от версии 2.0.0 и Java SDK версии 2.0.1 и далее.

До v2.5.0-beta2 ASP.NET SDK и v2.2.0-beta3 ASP.NET Core SDK, решение о выборке было основано на хэше идентификатора пользователя для приложений, определяющих «пользователя» (т.е. наиболее типичные веб-приложения). Для типов приложений, не определяющих пользователей (например, веб-сервисов), решение о выборке было основано на идентификаторе операции запроса. Последние версии ASP.NET и ASP.NET Core SDK используют идентификатор операции для решения о выборке.

## <a name="next-steps"></a>Дальнейшие действия

* [Фильтрация](../../azure-monitor/app/api-filtering-sampling.md) может обеспечивать более строгий контроль над данными, отправляемыми пакетом SDK.
* Читайте статью Developer Network [Оптимизировать телеметрию с application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
