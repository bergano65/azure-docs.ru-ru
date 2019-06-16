---
title: Счетчики производительности в Application Insights | Документация Майкрософт
description: Мониторинг системных и пользовательских счетчиков производительности .NET в Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255342"
---
# <a name="system-performance-counters-in-application-insights"></a>Системные счетчики производительности в Application Insights

В Windows предусмотрены самые разные [счетчики производительности](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), которые отображают показатели использования ЦП, памяти, диска и сети. Вы также можете определить собственные счетчики производительности. Сбор данных счетчиков производительности, поддерживается до тех пор, пока приложение запущено под управлением служб IIS на локальном узле или виртуальной машины, к которому имеется административный доступ. Хотя приложений, выполняющихся как веб-приложений Azure не имеют прямого доступа к счетчикам производительности, часть доступных счетчиков собираются службой Application Insights.

## <a name="view-counters"></a>Просмотр счетчиков

В колонке "Метрики" показан стандартный набор счетчиков производительности.

![Счетчики производительности, отображаемые в Application Insights](./media/performance-counters/performance-counters.png)

Текущий счетчики по умолчанию, настроенные для сбора данных по веб-приложений ASP.NET/ASP.NET Core являются:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

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
> Приложения ASP.NET Core, не имеют `ApplicationInsights.config`, и поэтому приведенный выше метод не является действительным для приложений ASP.NET Core.

Вы можете собирать показания как стандартных счетчиков, так и созданных вами самостоятельно. Пример стандартного счетчика `\Objects\Processes` доступен во всех системах Windows. Пример пользовательского счетчика, который можно реализовать в веб-службе: `\Sales(photo)\# Items Sold`.

Используется формат `\Category(instance)\Counter"`, а для категорий без экземпляров — просто `\Category\Counter`.

`ReportAs` — обязательный параметр для счетчиков, имена которых не соответствуют маске `[a-zA-Z()/-_ \.]+`, т. е. содержат любые символы, не входящие в этот стандартный набор: буквы, круглые скобки, косую черту, дефис, символ подчеркивания, пробел и точку.

При указании экземпляра он будет собираться в качестве измерения CounterInstanceName обнаруженной метрики.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Собирать данные счетчиков производительности в коде веб-приложений ASP.NET или.NET/.NET Core консольных приложений
Чтобы собрать данные счетчиков производительности системы и передать их в Application Insights, можно использовать следующий фрагмент кода:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

То же самое можно сделать с пользовательскими метриками, созданными вами:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Собирать данные счетчиков производительности в коде для веб-приложений ASP.NET Core

Изменить `ConfigureServices` метод в вашей `Startup.cs` класса, как показано ниже.

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

Схема **PerformanceCounters** предоставляет `category`, имя `counter` и имя `instance` каждого счетчика производительности.  В данных телеметрии для каждого приложения вы увидите только счетчики для этого приложения. Например, вот как можно увидеть, какие счетчики доступны. 

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

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Счетчики производительности для приложений, работающих в веб-приложений Azure

Приложения ASP.NET и ASP.NET Core, развернутые для веб-приложений Azure запускать в среде специальные "песочницы". Эта среда не поддерживает прямой доступ к счетчикам производительности системы. Тем не менее, ограниченный набор счетчиков доступны как переменные среды, как описано [здесь](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Пакет SDK Application Insights для ASP.NET и ASP.NET Core собирает счетчики производительности из веб-приложения Azure из этих специальных переменных среды. Только подмножество счетчики доступны в этой среде, и можно найти полный список [здесь.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Счетчики производительности в приложениях ASP.NET Core

* [Пакет SDK для ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) версии 2.4.1 и выше собирает счетчики производительности, если приложение выполняется в веб-приложения Azure (Windows)

* Пакет SDK версии 2.7.0-beta3 и выше собирает счетчики производительности, если приложение работает в Windows и предназначенных для `NETSTANDARD2.0` или более поздней версии.
* Для приложений, предназначенных для .NET Framework счетчики производительности поддерживаются во всех версиях пакета SDK.
* В этой статье будет обновляться при добавлении поддержки счетчика производительности в отличных от Windows.

## <a name="alerts"></a>Оповещения
Как и для других метрик, вы можете [установить оповещение](../../azure-monitor/app/alerts.md), которое предупредит о выходе показаний счетчика производительности за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="next"></a>Дальнейшие действия

* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)
* [Отслеживание исключений](../../azure-monitor/app/asp-net-exceptions.md)

