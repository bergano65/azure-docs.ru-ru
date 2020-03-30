---
title: Счетчики производительности в Application Insights | Документация Майкрософт
description: Мониторинг системных и пользовательских счетчиков производительности .NET в Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669885"
---
# <a name="system-performance-counters-in-application-insights"></a>Системные счетчики производительности в Application Insights

В Windows предусмотрены самые разные [счетчики производительности](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), которые отображают показатели использования ЦП, памяти, диска и сети. Вы также можете определить собственные счетчики производительности. Сбор счетчиков производительности поддерживается до тех пор, пока ваше приложение работает под IIS на месте размещения, или виртуальная машина, к которой у вас есть административный доступ. Хотя приложения, работающие как Azure Web Apps, не имеют прямого доступа к счетчикам производительности, подмножество доступных счетчиков собирается Insights Applications.

## <a name="view-counters"></a>Просмотр счетчиков

В колонке "Метрики" показан стандартный набор счетчиков производительности.

![Счетчики производительности, отображаемые в Application Insights](./media/performance-counters/performance-counters.png)

Текущие счетчики по умолчанию, настроенные для сбора ASP.NET/ASP.NET основных веб-приложений:
- %\\Время обработки
- %\\Время обработки процессора нормализовано
- Память\\доступна байтов
- запросы ASP.NET/Sec
- .NET CLR Исключения Брошенные / сек
- ASP.NET приложенияЗапрос времени выполнения
- Процесс\\частных байтов
- Обработка\\IO данных Байты / сек
- ASP.NET\\запросы заявок в очереди приложений
- Процессор (_Total)\\% Время процессора

## <a name="add-counters"></a>Добавление счетчиков

Если нужный счетчик производительности не включен в список метрик, можно добавить его.

1. Чтобы получить список счетчиков, доступных на сервере, выполните на локальном сервере такую команду PowerShell:

    `Get-Counter -ListSet *`

    (См. [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Откройте файл ApplicationInsights.config.

   * Если вы добавили Application Insights в приложение во время разработки, отредактируйте файл ApplicationInsights.config в проекте и повторно разверните его на серверах.
3. Измените директиву сборщика данных производительности:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET основных приложений не имеют, `ApplicationInsights.config`и, следовательно, вышеуказанный метод не действителен для ASP.NET основных приложений.

Вы можете собирать показания как стандартных счетчиков, так и созданных вами самостоятельно. Пример стандартного счетчика `\Objects\Processes` доступен во всех системах Windows. Пример пользовательского счетчика, который можно реализовать в веб-службе: `\Sales(photo)\# Items Sold`.

Используется формат `\Category(instance)\Counter"`, а для категорий без экземпляров — просто `\Category\Counter`.

`ReportAs` — обязательный параметр для счетчиков, имена которых не соответствуют маске `[a-zA-Z()/-_ \.]+`, т. е. содержат любые символы, не входящие в этот стандартный набор: буквы, круглые скобки, косую черту, дефис, символ подчеркивания, пробел и точку.

При указании экземпляра он будет собираться в качестве измерения CounterInstanceName обнаруженной метрики.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Сбор счетчиков производительности в коде для ASP.NET web-приложений или приложений основных консолей .NET/.NET
Чтобы собрать данные счетчиков производительности системы и передать их в Application Insights, можно использовать следующий фрагмент кода:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

То же самое можно сделать с пользовательскими метриками, созданными вами:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Сбор счетчиков производительности в коде для ASP.NET основных веб-приложений

Изменение `ConfigureServices` метода `Startup.cs` в классе, как ниже.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Счетчики производительности в службе аналитики
В [службе аналитики](../../azure-monitor/app/analytics.md) можно выполнять поиск отчетов по счетчикам производительности и отображать их.

Схема **performanceCounters** `category`раскрывает, `counter` имя и `instance` имя каждого счетчика производительности.  В данных телеметрии для каждого приложения вы увидите только счетчики для этого приложения. Например, вот как можно увидеть, какие счетчики доступны. 

![Счетчики производительности в аналитике Application Insights](./media/performance-counters/analytics-performance-counters.png)

("Экземпляр" здесь обозначает экземпляр счетчика производительности, а не экземпляр роли или сервера. Имя экземпляра счетчика производительности обычно разделяет такие счетчики, как загруженность процессора, по именам процессов или приложений.)

Вот как можно получить диаграмму доступной памяти за последний период. 

![Временная диаграмма памяти в аналитике Application Insights](./media/performance-counters/analytics-available-memory.png)

Как и другие данные телеметрии, данные **performanceCounters** также содержат столбец `cloud_RoleInstance`, который определяет экземпляр сервера, на котором выполняется приложение. Например, вот как можно сравнить производительность приложения на разных компьютерах. 

![Производительность, сегментированная по экземпляру роли в аналитике Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET и счетчики Application Insights

*Чем отличаются метрики "Частота исключений" и "Исключения"?*

* *Частота исключений* — это системный счетчик производительности. Среда CLR подсчитывает все обработанные и необработанные исключения и делит их общее количество в интервале выборки на продолжительность интервала. Пакет SDK Application Insights получает этот результат и отправляет его на портал.

* *Исключения* — это количество отчетов TrackException, полученных порталом в интервале выборки диаграммы. Включает только обработанные исключения, в коде которых прописаны вызовы TrackException, и не включает [необработанные исключения](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Счетчики производительности для приложений, работающих в веб-приложениях Azure

Как ASP.NET, так и ASP.NET основных приложений, развернутых в Web Apps Azure, работают в специальной среде песочницы. Эта среда не позволяет прямой доступ к счетчикам производительности системы. Тем не менее, ограниченный подмножество счетчиков подвергаются как переменные среды, как описано [здесь.](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables) Application Insights SDK для ASP.NET и ASP.NET Core собирает счетчики производительности из web-приложений Azure из этих специальных переменных среды. В этой среде доступно только подмножество счетчиков, а полный список можно найти [здесь.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Счетчики производительности в ASP.NET основных приложениях

Поддержка счетчиков производительности в ASP.NET Core ограничена:

* [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.4.1, а затем собирать счетчики производительности, если приложение работает в Azure Web Apps (Windows).
* SDK версии 2.7.1, а затем собирать счетчики производительности, если приложение работает в Windows и цели `NETSTANDARD2.0` или позже.
* Для приложений, ориентированных на рамочную систему .NET, все версии счетчиков производительности SDK поддерживаются.
* SDK Версии 2.8.0 и позже поддержка cpu / счетчик памяти в Linux. Ни один другой счетчик не поддерживается в Linux. Рекомендуемый способ получения счетчиков системы в Linux (и других средах, не связанных с Windows), заключается в использовании [EventCounters](eventcounters.md)

## <a name="alerts"></a>видны узлы
Как и для других метрик, вы можете [установить оповещение](../../azure-monitor/app/alerts.md), которое предупредит о выходе показаний счетчика производительности за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="next-steps"></a><a name="next"></a>Дальнейшие действия

* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)
* [Отслеживание исключений](../../azure-monitor/app/asp-net-exceptions.md)

