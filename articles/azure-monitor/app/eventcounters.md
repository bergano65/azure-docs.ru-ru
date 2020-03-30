---
title: Счетчики событий в ПриложенияХлоги (ru) Документы Майкрософт
description: Мониторинг системы и пользовательских .NET/.NET Основные EventCounters в application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663595"
---
# <a name="eventcounters-introduction"></a>Введение EventCounters

`EventCounter`является механизмом .NET/.NET Core для публикации и потребления счетчиков или статистики. [В этом](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) документе `EventCounters` приводится обзор и примеры того, как публиковать и потреблять их. EventCounters поддерживается на всех платформах ОС - Windows, Linux и macOS. Его можно рассматривать как кросс-платформенный эквивалент для [Счетчиков производительности,](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) который поддерживается только в системах Windows.

В то время `EventCounters` как пользователи могут публиковать любой пользовательский способ для удовлетворения своих потребностей, время выполнения .NET Core 3.0 публикует набор этих счетчиков по умолчанию. Документ будет проходить через шаги, `EventCounters` необходимые для сбора и просмотра (системы, определенной или пользователь определенной) в Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Использование приложений Insights для сбора EventCounters

Приложение Исследования поддерживает `EventCounters` сбор `EventCounterCollectionModule`с его , который является частью недавно выпустила nuget пакет [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`автоматически включается при использовании [aspNetCore](asp-net-core.md) или [WorkerService.](worker-service.md) `EventCounterCollectionModule`собирает счетчики с ненастраиваемой частотой сбора 60 секунд. Для сбора EventCounters не требуется специальных разрешений.

## <a name="default-counters-collected"></a>Собранные счетчики по умолчанию

Для приложений, работающих в .NET Core 3.0, следующие счетчики собираются автоматически SDK. Название счетчиков будет от формы «Категория» Счетчик".

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
> Счетчики категории Microsoft.AspNetCore.Hosting добавляются только в ASP.NET основных приложений.

## <a name="customizing-counters-to-be-collected"></a>Настройка счетчиков для сбора

Ниже приводится пример, как добавлять/удалять счетчики. Эта настройка будет выполнена в методе `ConfigureServices` вашего приложения после того, `AddApplicationInsightsTelemetry()` `AddApplicationInsightsWorkerService()`как коллекция телеметрии Application Insights будет включена с помощью либо или . Ниже приведен пример кода из приложения ASP.NET Core. Для других типов приложений обратитесь к [этому](worker-service.md#configuring-or-removing-default-telemetrymodules) документу.

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

## <a name="event-counters-in-metric-explorer"></a>Счетчики событий в Metric Explorer

Для просмотра метрик EventCounter в [Metric Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)выберите ресурс Application Insights и выберите метрики на основе журнала в качестве метрики. Затем метрики EventCounter отображаются в категории Пользовательские.

> [!div class="mx-imgBorder"]
> ![Счетчики событий, зарегистрированные в Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Счетчики событий в аналитике

Вы также можете искать и отображать контротчеты событий в [Analytics](../../azure-monitor/app/analytics.md), в таблице **customMetrics.**

Например, выполнить следующий запрос, чтобы узнать, какие счетчики собраны и доступны для запроса:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Счетчики событий, зарегистрированные в Application Insights](./media/event-counters/analytics-event-counters.png)

Чтобы получить диаграмму конкретного счетчика `ThreadPool Completed Work Item Count`(например: ) за последний период, запустите следующий запрос.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Чат одного счетчика в Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Как и в другой телеметрии, `cloud_RoleInstance` **customMetrics** также имеет столбец, указывающий на личность экземпляра сервера-хоста, на котором работает приложение. Вышеупомянутый запрос показывает встречное значение в экземпляре и может использоваться для сравнения производительности различных экземпляров сервера.

## <a name="alerts"></a>видны узлы
Как и другие метрики, можно [настроить оповещение,](../../azure-monitor/app/alerts.md) чтобы предупредить вас, если счетчик событий выходит за пределы указанного лимита. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Могу ли я просмотреть EventCounters в live Metrics?

Прямая метрика не показывает EventCounters на сегодняшний день. Используйте Metric Explorer или Analytics, чтобы увидеть телеметрию.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>На каких платформах можно увидеть список счетчиков .NET Core 3.0?

EventCounter не требует специальных разрешений и поддерживается на всех платформах.NET Core 3.0 поддерживается. В том числе:

* **Операционная система**: Windows, Linux или macOS.
* **Метод хостинга**: В процессе или вне процесса.
* **Метод развертывания**: Инфраструктура зависима или автономна.
* **Веб-сервер**: IIS (Интернет информационный сервер) или Kestrel.
* **Платформа хостинга**: Функция Web Apps службы приложений Azure, Azure VM, Docker, Azure Kubernetes Service (AKS) и так далее.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Я включил приложение Исследования из Azure Web App Портал. Но я не вижу EventCounters.?

 [Расширение Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) для ASP.NET Core пока не поддерживает эту функцию. Этот документ будет обновляться, когда эта функция будет поддерживаться.

## <a name="next-steps"></a><a name="next"></a>Дальнейшие действия

* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)
