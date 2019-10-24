---
title: Счетчики событий в Application Insights | Документация Майкрософт
description: Мониторинг системы и пользовательского Евенткаунтерс .NET и .NET Core в Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cithomas
ms.author: cithomas
ms.date: 09/20/2019
ms.openlocfilehash: 0762819239e8fd71a015f317776a94280806db53
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72677155"
---
# <a name="eventcounters-introduction"></a>Введение в Евенткаунтерс

`EventCounter` является механизмом .NET или .NET Core для публикации и использования счетчиков или статистики. В [этом](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) документе приводятся общие сведения о `EventCounters` и примеры их публикации и использования. Евенткаунтерс поддерживаются во всех платформах ОС — Windows, Linux и macOS. Его можно рассматривать как кросс-платформенный эквивалент для [счетчиков производительности](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) , который поддерживается только в системах Windows.

Хотя пользователи могут публиковать любые пользовательские `EventCounters` в соответствии с их потребностями, среда выполнения .NET Core 3,0 по умолчанию публикует набор этих счетчиков. В документе будут рассмотрены шаги, необходимые для получения и просмотра `EventCounters` (определяемые системой или определяемые пользователем) в Application Insights Azure.

## <a name="using-application-insights-to-collect-eventcounters"></a>Использование Application Insights для получения Евенткаунтерс

Application Insights поддерживает сбор `EventCounters` с его `EventCounterCollectionModule`, который входит в состав недавно выпущенного пакета NuGet [Microsoft. ApplicationInsights. евенткаунтерколлектор](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` автоматически включаются при использовании либо [AspNetCore](asp-net-core.md) , либо [воркерсервице](worker-service.md). `EventCounterCollectionModule` собирает счетчики с ненастраиваемой частотой сбора 60 секунд. Для получения Евенткаунтерс не требуются специальные разрешения.

## <a name="default-counters-collected"></a>Собранные счетчики по умолчанию

Для приложений, работающих в .NET Core 3,0, пакет SDK автоматически собирает следующие счетчики. Имена счетчиков будут иметь вид "Category | Счетчик ".

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
> Счетчики категории Microsoft. AspNetCore. Hosting добавляются только в приложениях Asp.Net Core.

## <a name="customizing-counters-to-be-collected"></a>Настройка собираемых счетчиков

В следующем примере показано, как добавить или удалить счетчики. Эта настройка будет выполнена в методе `ConfigureServices` приложения после включения Application Insights коллекции телеметрии с помощью `AddApplicationInsightsTelemetry()` или `AddApplicationInsightsWorkerService()`. Ниже приведен пример кода из приложения ASP.NET Core. Сведения о других типах приложений см. в [этом](worker-service.md#configuring-or-removing-default-telemetrymodules) документе.

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

Чтобы просмотреть метрики Евенткаунтер в [обозревателе метрик](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), выберите Application Insights ресурс и укажите метрики на основе журнала в качестве пространства имен метрик. Затем Евенткаунтер метрики отображаются в категории PerformanceCounter.

> [!div class="mx-imgBorder"]
> ![счетчики событий, о которых сообщается в Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Счетчики событий в аналитике

Отчеты счетчиков событий можно также искать и отображать в [аналитике](../../azure-monitor/app/analytics.md)в таблице **PerformanceCounters** .

Например, выполните следующий запрос, чтобы узнать, какие счетчики собираются и доступны для запроса:

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![счетчики событий, о которых сообщается в Application Insights](./media/event-counters/analytics-event-counters.png)

Чтобы получить диаграмму определенного счетчика (например: `ThreadPool Completed Work Item Count`) за последний период, выполните следующий запрос.

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![разговора одного счетчика в Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Как и другие данные телеметрии, данные **performanceCounters** также содержат столбец `cloud_RoleInstance`, который определяет экземпляр сервера, на котором выполняется приложение. Приведенный выше запрос показывает значение счетчика для каждого экземпляра и может использоваться для сравнения производительности различных экземпляров сервера.

## <a name="alerts"></a>Оповещения
Как и другие метрики, вы можете [настроить оповещение](../../azure-monitor/app/alerts.md) о том, что счетчик событий выходит за пределы указанного предела. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Можно ли увидеть Евенткаунтерс в динамических метриках?

Динамические метрики не показывают Евенткаунтерс на сегодняшний день. Используйте обозреватель метрик или аналитику для просмотра данных телеметрии.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Какие платформы можно увидеть список счетчиков .NET Core 3,0 по умолчанию?

Евенткаунтер не требует каких-либо специальных разрешений и поддерживается во всех платформах .NET Core 3,0. А именно:

* **Операционная система**: Windows, Linux или macOS.
* **Метод размещения**: в процессе или вне процесса.
* **Метод развертывания**: зависимая от платформы или автономная.
* **Веб-сервер**: IIS (Internet Information Server) или Kestrel.
* **Платформа размещения**: функции веб-приложений службы приложений Azure, виртуальной машины Azure, Docker, службы Azure KUBERNETES (AKS) и т. д.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Я включил Application Insights с портала веб-приложений Azure. Но я не вижу Евенткаунтерс.?

 [Расширение Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) для ASP.NET Core пока не поддерживает эту функцию. Этот документ будет обновлен, если эта функция поддерживается.

## <a name="next"></a>Дальнейшие действия

* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)