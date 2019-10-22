---
title: API Application Insights для пользовательских событий и метрик | Документация Майкрософт
description: Вставьте несколько строк кода в свое устройство или классическое приложение, на веб-страницу или в службу, чтобы отслеживать использование приложения и диагностировать неполадки.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/27/2019
ms.openlocfilehash: ca77cf6b838d2e10eab422f37ff55fbb43a2dba0
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677847"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API Application Insights для пользовательских событий и метрик

Вставьте несколько строк кода в свое приложение, чтобы узнать, как пользователи его используют, или чтобы диагностировать неполадки. Вы можете отправлять телеметрию из устройств и классических приложений, веб-клиентов и веб-серверов. Используйте основной API телеметрии [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md), чтобы отправлять пользовательские события и метрики, а также собственные версии стандартной телеметрии. Этот же API используется стандартными сборщиками данных Application Insights.

## <a name="api-summary"></a>Сводные данные API

Этот основной API используется на всех платформах, за некоторыми исключениями, например `GetMetric` (только в .NET).

| Метод | Область использования |
| --- | --- |
| [`TrackPageView`](#page-views) |Страницы, экраны, колонки или формы. |
| [`TrackEvent`](#trackevent) |Действия пользователя и другие события. Используется для отслеживания поведения пользователя или мониторинга производительности. |
| [`GetMetric`](#getmetric) |Нулевые и многомерные метрики, централизованное агрегирование, только в C#. |
| [`TrackMetric`](#trackmetric) |Измерения производительности, не связанные с конкретными событиями, например, измерение длины очереди. |
| [`TrackException`](#trackexception) |Регистрация исключений для диагностики. Отслеживайте исключения, связанные с другими событиями, и изучайте трассировку стека. |
| [`TrackRequest`](#trackrequest) |Регистрация частоты и длительности запросов к серверу для анализа производительности. |
| [`TrackTrace`](#tracktrace) |Сообщения журналов диагностики. Можно также использовать журналы сторонних приложений. |
| [`TrackDependency`](#trackdependency) |Регистрация длительности и частоты вызовов внешних компонентов, от которых зависит приложение. |

Вы можете [прикрепить свойства и метрики](#properties) к большинству этих вызовов телеметрии.

## <a name="prep"></a>Перед началом работы

Если у вас еще нет ссылки на пакет SDK Application Insights, выполните указанные ниже действия.

* Добавьте пакет SDK Application Insights в свой проект:

  * [проект ASP.NET;](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core проект](../../azure-monitor/app/asp-net-core.md)
  * [проект Java;](../../azure-monitor/app/java-get-started.md)
  * [проект Node.js;](../../azure-monitor/app/nodejs.md)
  * [JavaScript на каждой веб-странице.](../../azure-monitor/app/javascript.md) 
* В программный код устройства или веб-сервера необходимо добавить:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Получение экземпляра TelemetryClient

Получите экземпляр `TelemetryClient` (без использования JavaScript на веб-страницах).

Для приложений [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) и [не HTTP/Worker для приложений .NET и .NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) рекомендуется получить экземпляр `TelemetryClient` из контейнера внедрения зависимостей, как описано в соответствующей документации.

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

Класс TelemetryClient является потокобезопасным.

Для проектов ASP.NET и Java автоматически фиксируются входящие HTTP-запросы. Возможно, потребуется создать дополнительные экземпляры TelemetryClient для другого модуля вашего приложения. Например, у вас может быть один экземпляр TelemetryClient в классе промежуточного ПО для отчетов о событиях бизнес-логики. Можно задать свойства, такие как идентификатор пользователя (UserId) и идентификатор устройства (DeviceId), для идентификации компьютера. Эти данные прикрепляются ко всем событиям, отправляемым экземпляром.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

В проектах Node.js для создания экземпляра можно использовать команду `new applicationInsights.TelemetryClient(instrumentationKey?)`. Но эта команда рекомендуется, только если требуется конфигурация, отдельная от единственного экземпляра `defaultClient`.

## <a name="trackevent"></a>TrackEvent (Отслеживание событий)

В Application Insights *пользовательское событие* — это точка данных, которую можно отобразить как суммарное значение в [обозревателе метрик](../../azure-monitor/app/metrics-explorer.md), а также как отдельные значения на вкладке [поиска по журналу диагностики](../../azure-monitor/app/diagnostic-search.md). (Оно не связано с MVC и другими "событиями" платформы.)

Вставьте вызовы `TrackEvent` в код для подсчета различных событий: как часто пользователи выбирают определенный компонент, как часто они достигают определенных целей, а также как часто возникают те или иные ошибки.

Например, отправляйте событие в игровом приложении при каждом выигрыше пользователя:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Пользовательские события в службе аналитики

Данные телеметрии доступны в таблице `customEvents` в [службе аналитики Application Insights](analytics.md). Каждая строка представляет собой вызов `trackEvent(..)` в приложении.

Если действует [выборка](../../azure-monitor/app/sampling.md), свойство itemCount имеет значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackEvent() процесс выборки передал только один. Чтобы получить правильное количество пользовательских событий, следует использовать такой код, как `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Примеры

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric (Отслеживание метрик)

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric не является предпочтительным методом отправки метрик. Метрики всегда нужно предварительно агрегировать в течение определенного периода перед отправкой. Используйте одну из перегрузок GetMetric(..), чтобы получить объект метрики для доступа к возможностям предварительного агрегирования пакета SDK. При реализации собственной логики предварительной обработки можно использовать метод TrackMetric () для отправки итоговых статистических выражений. Если приложению требуется каждый раз отправить отдельный элемент телеметрии без агрегирования в течение определенного периода, скорее всего, у вас уже есть вариант использования телеметрии события; см. метод TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Application Insights может создать диаграмму метрик, не привязанных к определенным событиям. Например, можно отслеживать длину очереди через регулярные промежутки времени. Благодаря метрикам отдельные измерения менее интересны, чем вариации и тенденции, и поэтому статистические диаграммы полезны.

Для отправки метрик в Application Insights можно использовать API `TrackMetric(..)`. Отправить метрику можно двумя способами.

* Отдельное значение. Каждый раз при выполнении измерения в приложении вы отправляете соответствующее значение в Application Insights. Например, предположим, что имеется метрика, описывающая число элементов в контейнере. В течение некоторого периода времени вы сначала помещаете три элемента в контейнер, а затем удаляете два элемента. Соответственно, `TrackMetric` вызывается дважды: сначала передается значение `3`, а затем значение `-2`. Application Insights сохраняет оба значения от вашего имени.

* Агрегирование. При работе с метриками отдельные измерения редко представляют интерес. Вместо этого обычно важна сводка того, что произошло за определенный период времени. Такая сводка называется _агрегированием_. В приведенном выше примере сумма агрегированной метрики за период времени равна `1`, а число значений метрики — `2`. При использовании агрегирования `TrackMetric` вызывается только один раз для каждого периода времени и отправляются агрегированные значения. Это рекомендуемый подход, так как он может значительно снизить затраты и потери производительности благодаря отправке меньшего числа точек данных в Application Insights. При этом собирается вся важная информация.

### <a name="examples"></a>Примеры

#### <a name="single-values"></a>Отдельные значения

Отправка значения одной метрики

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Настраиваемые метрики в аналитике

Данные телеметрии доступны в таблице `customMetrics` в [службе аналитики Application Insights](analytics.md). Каждая строка представляет собой вызов `trackMetric(..)` в приложении.

* `valueSum` — это сумма измерений. Чтобы получить среднее значение, разделите текущее значение на значение `valueCount`.
* `valueCount` — число измерений, агрегированных в этот вызов `trackMetric(..)`.

## <a name="page-views"></a>Просмотры страниц

В устройстве или приложении веб-страницы телеметрия просмотров страницы отображается по умолчанию при загрузке каждого экрана или страницы. Однако это можно изменить, чтобы отслеживать количество просмотров страницы в дополнительное или другое время. Например, в приложении, которое отображает вкладки или колонки, может потребоваться отслеживать страницу каждый раз, когда пользователь открывает новую колонку.

Данные о пользователе и сеансе отправляются в качестве свойств вместе с количеством просмотров страниц, поэтому диаграммы для пользователей и сеансов активируются при наличии телеметрии по количеству просмотров страницы.

### <a name="custom-page-views"></a>Пользовательские данные о просмотре страницы

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Если у вас есть несколько вкладок на различных HTML-страницах, можно также указать URL-адрес:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Время просмотра страниц

По умолчанию время, отображаемое как **Время загрузки страницы**, отсчитывается от момента отправки запроса браузером до вызова события загрузки страницы в браузере.

Вместо этого вы можете выбрать один из таких вариантов:

* Задайте явную длительность вызова [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Используйте вызовы времени просмотра страницы `startTrackPage` и `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Имя, используемое в качестве первого параметра, связывает вызовы start и stop. По умолчанию используется имя текущей страницы.

Полученные длительности загрузки страниц, отображаемые в обозревателе метрик, являются производными интервала между вызовами start и stop. Выбор рассматриваемого интервала за вами.

### <a name="page-telemetry-in-analytics"></a>Телеметрия страниц в службе аналитики

В [службе аналитики](analytics.md) две таблицы содержат данные по операциям в браузере.

* Таблица `pageViews` содержит данные по URL-адресу и названию страницы.
* Таблица `browserTimings` содержит данные по производительности клиента, например время, затраченное на обработку входящих данных.

Чтобы узнать, сколько времени требуется браузеру на обработку различных страниц, используйте следующий код:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Чтобы установить популярность различных браузеров, используйте следующий код:

```kusto
pageViews
| summarize count() by client_Browser
```

Чтобы связать просмотры страниц с вызовами AJAX, выполните объединение с зависимостями:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest (Отслеживание запросов)

TrackRequest используется в серверном пакете SDK, чтобы регистрировать HTTP-запросы.

Его можно также вызвать самостоятельно, чтобы смодулировать запросы в контексте, в котором отсутствует выполняющийся модуль веб-службы.

Мы рекомендуем отправлять телеметрию запроса, выступающего в качестве <a href="#operation-context">контекста операции</a>.

## <a name="operation-context"></a>Контекст операции

Вы можете коррелировать элементы телеметрии между собой. Для этого свяжите их с контекстом операции. Стандартный модуль отслеживания запросов делает это для исключений и других событий, отправляемых во время обработки HTTP-запроса. В службе [Поиск](../../azure-monitor/app/diagnostic-search.md) и службе [Analytics](analytics.md) можно легко найти все события, связанные с запросом при помощи его идентификатора операции.

Дополнительные сведения о корреляции см. в статье [Корреляция данных телеметрии в Application Insights](../../azure-monitor/app/correlation.md).

Если вы отслеживаете телеметрию вручную, проще всего выполнить ее корреляцию с помощью этого шаблона:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Наряду с определением контекста операции `StartOperation` создает элемент телеметрии указанного вами типа и отправляет его при удалении операции или при явном вызове `StopOperation`. При использовании типа телеметрии `RequestTelemetry` ее длительность задается как временной интервал между запуском и остановкой.

Элементы телеметрии, о которых поступают сведения в пределах операции, становятся дочерними элементами такой операции. Контексты операции могут быть вложенными.

В колонке поиска контекст операции используется для создания списка **связанных элементов**.

![Связанные элементы](./media/api-custom-events-metrics/21.png)

Дополнительные сведения об отслеживании пользовательских операций см. в статье [Отслеживание пользовательских операций с помощью пакета SDK Application Insights для .NET](../../azure-monitor/app/custom-operations-tracking.md).

### <a name="requests-in-analytics"></a>Запросы в аналитике

В [службе аналитики Application Insights](analytics.md) запросы приводятся в таблице `requests`.

Если действует [выборка](../../azure-monitor/app/sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackRequest() процесс выборки передал только один. Чтобы получить правильное число запросов и среднюю длительность, разбитую по именам запросов, используйте следующий код:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException (Отслеживание исключений)

Отправляйте исключения в Application Insights, чтобы:

* [определить их количество](../../azure-monitor/app/metrics-explorer.md) (для указания на частоту возникновения проблемы);
* [проверить отдельные значения.](../../azure-monitor/app/diagnostic-search.md)

Отчеты содержат данные по трассировкам стеков.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Пакеты SDK перехватывают многие исключения автоматически, поэтому не всегда нужно явно вызвать метод TrackException.

* ASP.NET: [написание кода для перехвата исключений](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [исключения перехватываются автоматически](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: исключения перехватываются автоматически. Если вы хотите отключить автоматический сбор, добавьте строку в фрагмент кода, который вставляется на веб-страницы.

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Исключения в службе аналитики

В [службе аналитики Application Insights](analytics.md) исключения приводятся в таблице `exceptions`.

Если действует [выборка](../../azure-monitor/app/sampling.md), свойство `itemCount` имеет значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackException() процесс выборки передал только один. Чтобы получить правильное число исключений, разбитое по типам исключений, используйте следующий код:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Большая часть важных сведений о стеке уже извлечена в отдельные переменные, однако вы можете разобрать структуру `details`, чтобы получить дополнительные сведения. Так как это динамическая структура, результат следует привести к требуемому типу. Пример.

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Чтобы связать исключения с соответствующими запросами, используйте соединение:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace (Отслеживание трассировки)

Используйте TrackTrace для диагностики неполадок, отправляя навигационную цепочку в Application Insights. Вы можете отправлять блоки диагностических данных и проверять их с помощью [поиска по журналу диагностики](../../azure-monitor/app/diagnostic-search.md).

В .NET [адаптеры журнала](../../azure-monitor/app/asp-net-trace-logs.md) используют этот API для отправки журналов сторонних производителей на портал.

В Java для [стандартных средств ведения журнала, например Log4J и Logback,](../../azure-monitor/app/java-trace-logs.md) используются аппендеры Application Insights Log4J или Logback, позволяющие отправлять журналы сторонних производителей на портал.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript на стороне клиента или браузера*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Зарегистрируйте событие диагностики, например вход в метод или выход из него.

 Параметр | Описание
---|---
`message` | Диагностические данные Могут содержать не только имя.
`properties` | Преобразование строки в строку: дополнительные данные, используемые для [фильтрации исключений](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) на портале. Значение по умолчанию: empty.
`severityLevel` | Поддерживаемые значения: [северитилевел. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Вы можете выполнять поиск содержимого сообщения, но (в отличие от значений свойств) не можете фильтровать его.

Ограничения по размеру `message` гораздо выше, чем ограничение для свойств.
Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных, например данных POST.  

Кроме того, вы можете настроить для сообщения уровень серьезности. Как и для других данных телеметрии, вы можете добавлять значения свойства, используемые для фильтрации или поиска различных наборов трассировки. Пример.

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

В колонке [Поиск](../../azure-monitor/app/diagnostic-search.md) вы сможете легко отфильтровать все сообщения с определенной степенью серьезности, относящиеся к определенной базе данных.

### <a name="traces-in-analytics"></a>Трассировки в службе аналитики

В [службе аналитики Application Insights](analytics.md) вызовы TrackTrace приводятся в таблице `traces`.

Если действует [выборка](../../azure-monitor/app/sampling.md), свойство itemCount имеет значение больше 1. Например, itemCount==10 означает, что из 10 вызовов `trackTrace()` процесс выборки передал только один. Чтобы получить правильное количество вызовов трассировки, следует использовать такой код, как `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency (Отслеживание зависимостей)

Используйте вызов TrackDependency для отслеживания времени отклика и процента успешных попыток вызовов во внешнюю часть кода. Результаты отображаются в диаграммах зависимостей на портале.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Помните, что серверные пакеты SDK включают [модуль зависимостей](../../azure-monitor/app/asp-net-dependencies.md), который автоматически обнаруживает и отслеживает определенные вызовы зависимостей, например вызовы к базам данных и интерфейсам REST API. Для работы модуля необходимо установить агент на сервере. 

В Java некоторые вызовы зависимостей могут автоматически отслеживаться с помощью [агента Java](../../azure-monitor/app/java-agent.md).

Используйте этот вызов, если необходимо отследить вызовы, которые не отслеживаются автоматически, или если вы не хотите устанавливать агент.

Чтобы отключить стандартный модуль отслеживания зависимостей в C#, измените файл [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) и удалите ссылку на `DependencyCollector.DependencyTrackingTelemetryModule`. В Java не следует устанавливать агент Java, если не требуется автоматически собирать данные стандартных зависимостей.

### <a name="dependencies-in-analytics"></a>Зависимости в службе аналитики

В [службе аналитики Application Insights](analytics.md) вызовы trackDependency приводятся в таблице `dependencies`.

Если действует [выборка](../../azure-monitor/app/sampling.md), свойство itemCount имеет значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackDependency() процесс выборки передал только один. Чтобы получить правильное число зависимостей, разбитое по конечным компонентам, используйте следующий код:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Чтобы связать зависимости с соответствующими запросами, используйте соединение:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Очистка данных

Как правило, пакет SDK отправляет данные через фиксированные интервалы (обычно 30 с) или каждый раз, когда буфер полон (обычно 500 элементов). Однако в некоторых случаях может потребоваться очистить буфер, например, при использовании пакета SDK в приложении, которое завершает работу.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Эта функция является асинхронной для [канала телеметрии сервера](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Желательно использовать в действии завершения работы для приложения метод flush().

## <a name="authenticated-users"></a>Прошедшие проверку пользователи

В веб-приложении пользователи по умолчанию идентифицируются файлами cookie. Пользователь может быть учтен более одного раза при доступе к приложению с другого компьютера или браузера либо при удалении файлов cookie.

Если пользователи входят в ваше приложение, более точное их количество можно узнать, указав идентификатор пользователя, прошедшего аутентификацию, в коде браузера.

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Например, в веб-приложении MVC ASP.NET.

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Нет необходимости использовать фактическое учетное имя пользователя. Нужен только уникальный идентификатор этого пользователя. В нем не должно быть пробелов или символов `,;=|`.

Идентификатор пользователя также задается в файле cookie сеанса и отправляется на сервер. Если установлен серверный пакет SDK, идентификатор пользователя, прошедшего проверку подлинности, отправляется как часть свойств контекста телеметрии клиента и сервера. Затем можно выполнить фильтрацию и поиск.

Если приложение группирует пользователей по учетным записям, вы также можете передать идентификатор учетной записи (с аналогичными ограничениями знаков).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

В [обозревателе метрик](../../azure-monitor/app/metrics-explorer.md) можно создать диаграмму для отображения количества **пользователей, прошедших проверку подлинности**, а также **учетных записей пользователей**.

Кроме того, можно [выполнить поиск](../../azure-monitor/app/diagnostic-search.md) точек данных клиентов с определенными именами пользователей и учетными записями.

## <a name="properties"></a>Фильтрация, поиск и сегментация данных с помощью свойств

Вы можете прикрепить свойства и результаты измерений к своим событиям, а также метрикам, просмотрам страниц, исключениям и другим данным телеметрии.

*Свойства* — это строковые значения, которые можно использовать для фильтрации телеметрии в отчетах об использовании. Например, если приложение содержит несколько игр, можно к каждому событию присоединять имя игры, чтобы видеть, какие игры наиболее популярны.

В каждой строке может отображаться до 8192 событий. (Если вы хотите отправлять большие блоки данных, используйте параметр сообщения TrackTrace.)

*Метрики* являются числовыми значениями, которые могут быть представлены в графическом виде. Например, вам нужно будет посмотреть, постепенно ли увеличиваются зарабатываемые игроками очки. Графы можно сегментировать по свойствам, отправленным с событием, чтобы получать отдельные графы или набор граф для разных игр.

Чтобы значения метрик отображались правильно, они должны быть больше или равны 0.

Количество используемых [свойств, значений свойств и метрик ограничено](#limits) .

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Постарайтесь не указывать в свойствах личные сведения.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Альтернативный способ настройки свойств и метрик

Для удобства вы можете собирать параметры события в отдельный объект:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Не используйте один и тот же экземпляр элемента телеметрии (в этом примере `event`) для многократного вызова Track*(). Это может привести к отправке телеметрии с неверной конфигурацией.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Пользовательские измерения и свойства в службе аналитики

В [службе аналитики](analytics.md) пользовательские метрики и свойства приводятся в атрибутах `customMeasurements` и `customDimensions` каждой записи телеметрии.

Например, если вы добавили свойство game в телеметрию запросов, следующий запрос подсчитывает число различных значений свойства game и показывает среднее значение пользовательской метрики score:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Обратите внимание на указанные ниже моменты.

* При извлечении значения из JSON customDimensions или customMeasurements оно имеет динамический тип, поэтому его необходимо привести к `tostring` или `todouble`.
* С учетом возможности [выборки](../../azure-monitor/app/sampling.md) следует использовать `sum(itemCount)`, а не `count()`.

## <a name="timed"></a> События времени

Иногда требуется отобразить на диаграмме продолжительность выполнения действия. Например, может потребоваться определить, сколько времени нужно пользователям для выбора решений в игре. Для этого можно использовать параметр измерения.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Свойства по умолчанию для настраиваемой телеметрии

Если для некоторых создаваемых пользовательских событий требуется задать значения свойств по умолчанию, можно сделать это в экземпляре TelemetryClient. Они прикреплены к каждому элементу телеметрии, отправляемой из этого клиента.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Вызовы отдельных данных телеметрии могут переопределить значения по умолчанию в словарях свойства.

*Для веб-клиентов JavaScript* используйте инициализаторы телеметрии JavaScript.

*Чтобы добавить свойства для всей телеметрии*, включая данные из модулей стандартной коллекции, [реализуйте `ITelemetryInitializer`](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Выборка, фильтрация и обработка данных телеметрии

Для обработки телеметрии перед отправкой из пакета SDK можно написать код. Будут обрабатываться данные, отправляемые из модулей стандартной телеметрии, таких как коллекция запросов HTTP и коллекция зависимостей.

[Добавьте свойства](../../azure-monitor/app/api-filtering-sampling.md#add-properties) в телеметрию, реализовав `ITelemetryInitializer`. Например, можно добавить номера версий или значения, вычисленные на основе других свойств.

Используя [фильтрацию](../../azure-monitor/app/api-filtering-sampling.md#filtering), можно изменить или отменить телеметрию перед ее отправкой из пакета SDK. Для этого реализуйте `ITelemetryProcessor`. Вы сможете управлять тем, какие данные отправляются, а какие отклоняются. Однако при этом необходимо учитывать влияние на метрики. В зависимости от способа отклонения элементов можно потерять возможность перехода между связанными элементами.

[Выборка](../../azure-monitor/app/api-filtering-sampling.md) представляет собой упакованное решение для сокращения объема данных, отправляемых из приложения на портал. На отображаемые метрики выборка не влияет. Возможность диагностировать проблемы путем перехода между связанными элементами, такими как исключения, запросы и просмотры страниц, также не затрагивается.

[Узнайте больше](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Отключение телеметрии

Чтобы *динамически остановить и запустить* сбор и передачу данных телеметрии:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Чтобы *Отключить выбранные стандартные собирающие*, например счетчики производительности, HTTP-запросы или зависимости, удалите или закомментируйте соответствующие строки в [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Это можно сделать, например, если вы хотите отправить собственные данные TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Чтобы *отключить выбранные стандартные сборщики*, например счетчики производительности, HTTP-запросы или зависимости, свяжите методы конфигурации с кодом инициализации пакета SDK во время инициализации.

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Чтобы отключить эти сборщики после инициализации, используйте объект конфигурации `applicationInsights.Configuration.setAutoCollectRequests(false)`.

## <a name="debug"></a>Режим разработчика

Во время отладки полезно передавать телеметрию через конвейер, чтобы результаты можно было увидеть немедленно. Кроме того, вы можете получать дополнительные сообщения, которые помогают трассировать любые проблемы с телеметрией. Отключите этот режим в рабочей среде, так как он может замедлить работу приложения.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Для Node. js можно включить режим разработчика, включив внутреннее ведение журнала с помощью `setInternalLogging` и установив `maxBatchSize` равным 0, что приводит к отправке данных телеметрии сразу после сбора.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a> Установка ключа инструментирования для выбранной пользовательской телеметрии

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Динамический ключ инструментирования

Во избежание смешивания телеметрии из среды разработки, тестовой и рабочей среды вы можете [создавать отдельные ресурсы Application Insights](../../azure-monitor/app/create-new-resource.md ) и менять их ключи в зависимости от среды.

Вместо получения ключа инструментирования из файла конфигурации можно задать его в коде. Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

На веб-странице может потребоваться задать его, используя состояние веб-сервера, а не внедрить его в сценарий. Например, на веб-странице, созданной в приложении ASP.NET.

*JavaScript в Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>Класс TelemetryContext

Экземпляр TelemetryClient включает свойство Context, содержащее несколько значений, которые отправляются вместе со всеми данными телеметрии. Как правило, их задают модули стандартной телеметрии, но их также можно задать самостоятельно. Пример.

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Если задать эти значения самостоятельно, мы советуем удалить соответствующую строку из файла [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), чтобы не перепутать собственные значения и стандартные значения.

* **Component:** приложение и его версия.
* **Device:** данные об устройстве, на котором выполняется приложение. (В веб-приложениях это сервер или клиентское устройство, с которых отправляется телеметрия.)
* **InstrumentationKey**: ресурс Application Insights в Azure, в котором отображается телеметрии. Обычно этот ресурс получают из файла ApplicationInsights.config.
* **Location:** географическое расположение устройства.
* **Operation:** текущий HTTP-запрос в веб-приложениях. В приложениях других типов для этого значения можно задать значение "Группировать события совместно".
  * **ID**: созданное значение, которое сопоставляет различные события, чтобы при проверке любого события в поиске по журналу диагностики можно было найти связанные элементы.
  * **Name**: идентификатор, обычно URL-адрес HTTP-запроса.
  * **SyntheticSource:** если эта строка не пустая и не имеет значение null, она означает, что источник запроса был определен как программа-робот или веб-тест. По умолчанию он исключается из вычислений в обозревателе метрик.
* **Properties:** свойства, которые отправляются со всеми данными телеметрии. Это значение можно переопределить в отдельных вызовах Track*.
* **Session:** сеанс пользователя. Для Id задается созданное значение, которое изменяется, если пользователь был неактивным в течение некоторого времени.
* **User:** информация о пользователе.

## <a name="limits"></a>Ограничения

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Чтобы избежать превышения ограничения на скорость передачи данных, используйте [выборку](../../azure-monitor/app/sampling.md).

Сведения о том, как определить, как долго хранятся данные, см. в статье [Сбор и хранение данных в Application Insights](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Справочные документы

* [Справочник по ASP.NET](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Справочник по Java](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [Справочник по JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Код пакета SDK

* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Пакеты Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Пакет SDK для Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Пакет SDK для Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Вопросы

* *Какие исключения могут создаваться при вызовах Track_()?*

    Нет подходящих вариантов. Вам не нужно помещать их в предложения try-catch. Если пакет SDK сталкивается с проблемами, он добавляет в журнал сообщения, которые отображаются в консоли отладки и, если сообщения проходят, — в поиске по журналу диагностики.
* *Существует ли REST API для получения данных из портала?*

    Да, [API доступа к данным](https://dev.applicationinsights.io/). К другим способам извлечения данных относятся [экспорт из Analytics в Power BI](../../azure-monitor/app/export-power-bi.md ) и [непрерывный экспорт](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Дальнейшие действия

* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md)
* [Устранение неполадок](../../azure-monitor/app/troubleshoot-faq.md)
