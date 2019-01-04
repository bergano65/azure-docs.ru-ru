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
ms.openlocfilehash: d5c7e1815fe27d9b851407a8e6f17d04d7bc1cad
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004725"
---
# <a name="system-performance-counters-in-application-insights"></a>Системные счетчики производительности в Application Insights

В Windows предусмотрены самые разные [счетчики производительности](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), которые отображают показатели использования ЦП, памяти, диска и сети. Вы также можете определить собственные счетчики производительности. До тех пор, пока приложение работает под управлением службы IIS на локальном узле или виртуальной машине, к которой у вас есть доступ с правами администратора.

## <a name="view-counters"></a>Просмотр счетчиков

В колонке "Метрики" показан стандартный набор счетчиков производительности.

![Счетчики производительности, отображаемые в Application Insights](./media/performance-counters/performance-counters.png)

Текущие счетчики по умолчанию, которые собраны для веб-приложений .NET:

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

Чтобы собрать в одном месте все важнейшие диаграммы, создайте [панель мониторинга](../../azure-monitor/app/app-insights-dashboards.md) и закрепите на ней нужные диаграммы.

## <a name="add-counters"></a>Добавление счетчиков

Если нужный счетчик производительности не включен в список метрик, можно добавить его.

1. Чтобы получить список счетчиков, доступных на сервере, выполните на локальном сервере такую команду PowerShell:
   
    `Get-Counter -ListSet *`
   
    (См. [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Откройте файл ApplicationInsights.config.
   
   * Если вы добавили Application Insights в приложение во время разработки, отредактируйте файл ApplicationInsights.config в проекте и повторно разверните его на серверах.
   * Если монитор состояний использовался как инструментарий для веб-приложения во время выполнения, файл ApplicationInsights.config вы найдете в корневом каталоге приложения в IIS. Обновите его в этом расположении на каждом экземпляре сервера.
3. Измените директиву сборщика данных производительности:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Вы можете собирать показания как стандартных счетчиков, так и созданных вами самостоятельно. Пример стандартного счетчика `\Objects\Processes` доступен во всех системах Windows. Пример пользовательского счетчика, который можно реализовать в веб-службе: `\Sales(photo)\# Items Sold`. 

Используется формат `\Category(instance)\Counter"`, а для категорий без экземпляров — просто `\Category\Counter`.

`ReportAs` — обязательный параметр для счетчиков, имена которых не соответствуют маске `[a-zA-Z()/-_ \.]+`, т. е. содержат любые символы, не входящие в этот стандартный набор: буквы, круглые скобки, косую черту, дефис, символ подчеркивания, пробел и точку.

При указании экземпляра он будет собираться в качестве измерения CounterInstanceName обнаруженной метрики.

### <a name="collecting-performance-counters-in-code"></a>Сбор данных счетчиков производительности в коде
Чтобы собрать данные счетчиков производительности системы и передать их в Application Insights, можно использовать следующий фрагмент кода:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
То же самое можно сделать с пользовательскими метриками, созданными вами:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
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

## <a name="performance-counters-in-aspnet-core-applications"></a>Счетчики производительности в приложениях ASP.NET Core
Счетчики производительности поддерживаются, только если приложение предназначено для полной версии платформы .NET Framework. Невозможно выполнять сбор данных счетчиков производительности для приложений .Net Core.

## <a name="alerts"></a>Оповещения
Как и для других метрик, вы можете [установить оповещение](../../azure-monitor/app/alerts.md), которое предупредит о выходе показаний счетчика производительности за установленные пределы. Откройте колонку "Оповещения" и щелкните "Добавить оповещение".

## <a name="next"></a>Дальнейшие действия
* [Отслеживание зависимостей](../../azure-monitor/app/asp-net-dependencies.md)
* [Отслеживание исключений](../../azure-monitor/app/asp-net-exceptions.md)

