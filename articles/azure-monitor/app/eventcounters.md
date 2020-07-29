---
title: Счетчики событий в Application Insights | Документация Майкрософт
description: Мониторинг системных и пользовательских объектов EventCounter платформы .NET/.NET Core в Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 42140f68a5f383a2a60fe8327f5023754366e6b7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324409"
---
# <a name="eventcounters-introduction"></a>Знакомство с объектами EventCounter

`EventCounter` (счетчик событий) — это механизм .NET и .NET Core для публикации и использования счетчиков или статистики. [Этот](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) документ содержит общие сведения об объектах `EventCounters` и примеры того, как их следует публиковать и использовать. Счетчики событий (объекты EventCounter) поддерживаются во всех платформах ОС — Windows, Linux и macOS. Их можно рассматривать как кроссплатформенный эквивалент объектов [PerformanceCounter](/dotnet/api/system.diagnostics.performancecounter), которые поддерживаются только в Windows-системах.

Хотя пользователи могут публиковать любые пользовательские объекты `EventCounters` в соответствии со своими потребностями, среда выполнения .NET Core 3.0 по умолчанию публикует набор этих счетчиков. В этом документе будут пошагово рассмотрены все действия, необходимые для сбора и просмотра объектов `EventCounters` (определенных системой или пользователем) в службе Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Использование Application Insights для сбора объектов EventCounter

Application Insights поддерживает сбор объектов `EventCounters` с помощью своего модуля `EventCounterCollectionModule`, который входит в состав недавно выпущенного пакета NuGet — [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). Модуль `EventCounterCollectionModule` автоматически включается при использовании [AspNetCore](asp-net-core.md) или [WorkerService](worker-service.md). `EventCounterCollectionModule` собирает счетчики с частотой 1 раз в 60 секунд (эту частоту сбора нельзя изменить). Для сбора объектов EventCounter не нужны специальные разрешения.

## <a name="default-counters-collected"></a>Счетчики, собираемые по умолчанию

Для приложений, работающих в .NET Core 3.0, пакет SDK автоматически собирает следующие счетчики. Имена счетчиков будут иметь вид "Категория | Счетчик".

|Категория | Счетчик|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Счетчики категории Microsoft. AspNetCore.Hosting добавляются только в приложениях ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Настройка собираемых счетчиков

В следующем примере показано, как следует добавлять или удалять счетчики. Эта настройка выполняется в методе `ConfigureServices` вашего приложения после того, как будет включен сбор данных телеметрии Application Insights с помощью метода `AddApplicationInsightsTelemetry()` или `AddApplicationInsightsWorkerService()`. Ниже приведен пример кода из приложения для ASP.NET Core. Информацию о других типах приложений можно найти в [этом](worker-service.md#configuring-or-removing-default-telemetrymodules) документе.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Счетчики событий в обозревателе метрик

Чтобы просмотреть метрики EventCounter в [обозревателе метрик](../platform/metrics-charts.md), выберите ресурс Application Insights и в качестве пространства имен метрик выберите метрики на основе журнала. После этого метрики EventCounter отобразятся в пользовательской категории.

> [!div class="mx-imgBorder"]
> ![Счетчики событий, отображаемые в Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Счетчики событий в службе аналитики

Отчеты по счетчикам событий также можно искать и просматривать в [службе аналитики](../log-query/log-query-overview.md), в таблице **customMetrics**.

Выполните, к примеру, следующий запрос, чтобы посмотреть, какие счетчики собираются и доступны для запроса:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Счетчики событий, отображаемые в Application Insights](./media/event-counters/analytics-event-counters.png)

Чтобы получить диаграмму для определенного счетчика (например, `ThreadPool Completed Work Item Count`) за последний период времени, выполните следующий запрос.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Диаграмма для одного счетчика в Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Как и другие данные телеметрии, данные **customMetrics** также содержат столбец `cloud_RoleInstance`, который указывает идентификатор экземпляра сервера, на котором выполняется приложение. Приведенный выше запрос показывает значение счетчика для каждого экземпляра и может использоваться для сравнения производительности различных экземпляров сервера.

## <a name="alerts"></a>видны узлы
Как и для других метрик, вы можете [задать оповещение](../platform/alerts-log.md), которое предупредит вас о том, что значение счетчика событий выходит за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Можно ли просматривать объекты EventCounter в динамических метриках?

В настоящее время в динамических метриках нельзя просматривать объекты EventCounter. Для просмотра данных телеметрии используйте обозреватель метрик или службу аналитики.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>На каких платформах можно увидеть стандартный список счетчиков .NET Core 3.0?

Объект EventCounter не требует никаких особых разрешений и поддерживается на всех платформах, в том числе на .NET Core 3.0. В том числе:

* **Операционная система**. Windows, Linux или macOS.
* **Метод размещения**. Внутри или вне процесса.
* **Метод развертывания**. Зависит от платформы либо автономный.
* **Веб-сервер**. IIS (Internet Information Server) или Kestrel.
* **Платформа размещения**. Функция веб-приложений службы приложений Azure, виртуальная машина Azure, Docker, служба Azure Kubernetes (AKS) и т. д.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Служба Application Insights была включена на портале веб-приложений Azure. Но почему я не вижу счетчики событий (объекты EventCounter)?

 [Расширение Application Insights](./azure-web-apps.md) для ASP.NET Core пока не поддерживает эту функцию. После того как эта функция начнет поддерживаться, соответствующая информация появится в этом документе.

## <a name="next-steps"></a><a name="next"></a>Следующие шаги

* [Отслеживание зависимостей](./asp-net-dependencies.md)

