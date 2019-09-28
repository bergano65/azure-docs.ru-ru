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
ms.openlocfilehash: fa4e45416e83d933cd21fe482bcead14bfbcae22
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349923"
---
# <a name="system-performance-counters-in-application-insights"></a>Системные счетчики производительности в Application Insights

В Windows предусмотрены самые разные [счетчики производительности](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), которые отображают показатели использования ЦП, памяти, диска и сети. Вы также можете определить собственные счетчики производительности. Сбор счетчиков производительности поддерживается при условии, что приложение выполняется в службах IIS на локальном узле или на виртуальной машине, к которой у вас есть административный доступ. Хотя приложения, работающие как веб-приложения Azure, не имеют прямого доступа к счетчикам производительности, подмножество доступных счетчиков собирается Application Insights.

## <a name="view-counters"></a>Просмотр счетчиков

В колонке "Метрики" показан стандартный набор счетчиков производительности.

![Счетчики производительности, отображаемые в Application Insights](./media/performance-counters/performance-counters.png)

Текущие счетчики по умолчанию, настроенные для сбора для веб-приложений ASP.NET/ASP.NET Core,:
- % Process @ no__t — время 0Processor
- % Process @ no__t-0Processorное время нормализовано
- Память @ no__t-0Available байт
- Запросов ASP.NET в секунду
- Вызвано исключений CLR .NET/с
- ASP.NET Аппликатионсрекуест время выполнения
- Процесс @ no__t-0Private байт
- Процесс @ no__t — 0IO данных, байт/с
- ASP.NET Applications @ no__t-0Requests в очереди приложений
- Процессор (_Total) \\% загруженности процессора

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
> ASP.NET Core приложения не имеют `ApplicationInsights.config`, поэтому приведенный выше метод не является допустимым для ASP.NET Core приложений.

Вы можете собирать показания как стандартных счетчиков, так и созданных вами самостоятельно. Пример стандартного счетчика `\Objects\Processes` доступен во всех системах Windows. Пример пользовательского счетчика, который можно реализовать в веб-службе: `\Sales(photo)\# Items Sold`.

Используется формат `\Category(instance)\Counter"`, а для категорий без экземпляров — просто `\Category\Counter`.

`ReportAs` — обязательный параметр для счетчиков, имена которых не соответствуют маске `[a-zA-Z()/-_ \.]+`, т. е. содержат любые символы, не входящие в этот стандартный набор: буквы, круглые скобки, косую черту, дефис, символ подчеркивания, пробел и точку.

При указании экземпляра он будет собираться в качестве измерения CounterInstanceName обнаруженной метрики.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Сбор счетчиков производительности в коде для веб-приложений ASP.NET или консольных приложений .NET и .NET Core
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

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Сбор счетчиков производительности в коде для веб-приложений ASP.NET Core

Измените метод `ConfigureServices` в классе `Startup.cs`, как показано ниже.

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

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Счетчики производительности для приложений, работающих в веб-приложениях Azure

Приложения ASP.NET и ASP.NET Core, развернутые в веб-приложениях Azure, выполняются в специальной изолированной среде. Эта среда не разрешает прямой доступ к счетчикам производительности системы. Однако ограниченное подмножество счетчиков предоставляется в виде переменных среды, как описано [здесь](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK для ASP.NET и ASP.NET Core собирает счетчики производительности из веб-приложений Azure из этих специальных переменных среды. В этой среде доступно только подмножество счетчиков. полный список можно найти [здесь.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Счетчики производительности в ASP.NET Core приложениях

Поддержка счетчиков производительности в ASP.NET Core ограничена:

* Версии [пакета SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 и более поздних версий собираются счетчики производительности, если приложение выполняется в веб-приложениях Azure (Windows).
* Версии пакета SDK 2.7.1 и более поздних версий собираются счетчики производительности, если приложение `NETSTANDARD2.0` выполняется в Windows и целевые или более поздние версии.
* Для приложений, предназначенных для .NET Framework, все версии пакетов SDK поддерживают счетчики производительности.
* Версии пакета SDK 2.8.0 и более поздней поддерживают счетчик ЦП/памяти в Linux. В Linux не поддерживается никакой другой счетчик. Рекомендуемый способ получения системных счетчиков в Linux (и других средах, отличных от Windows) — с помощью [евенткаунтерс](eventcounters.md)

## <a name="alerts"></a>Предупреждения
Как и для других метрик, вы можете [установить оповещение](../../azure-monitor/app/alerts.md), которое предупредит о выходе показаний счетчика производительности за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="next"></a>Дальнейшие действия

* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)
* [Отслеживание исключений](../../azure-monitor/app/asp-net-exceptions.md)

