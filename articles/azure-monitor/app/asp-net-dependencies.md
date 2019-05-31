---
title: Отслеживание зависимостей в Azure Application Insights | Документация Майкрософт
description: Отслеживайте вызовы зависимостей от локальной или веб-приложения Microsoft Azure с помощью Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299278"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Отслеживание зависимостей в Azure Application Insights 

*Зависимость* – это внешний компонент, который вызывается приложением. Как правило, это служба, вызываемая с использованием HTTP, база данных или файловая система. [Application Insights](../../azure-monitor/app/app-insights-overview.md) измеряет длительность вызовов зависимостей, является ли его сбоем или нет, а также дополнительные сведения, такие как имя зависимости и т. д. Можно исследовать вызовы конкретных зависимостей и их корреляции запросов и исключений.

## <a name="automatically-tracked-dependencies"></a>Автоматически отслеживаемых зависимостей

Пакеты SDK Application Insights для .NET и .NET Core поставляется с `DependencyTrackingTelemetryModule` модуля телеметрии, эта функция автоматически собирает зависимости. Эта коллекция зависимостей включается автоматически для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) приложения при соответствии с связанного официальной документации. `DependencyTrackingTelemetryModule` поставляется как [это](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) пакет NuGet и будет подключен к автоматически при использовании любого из пакетов NuGet `Microsoft.ApplicationInsights.Web` или `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` в настоящее время автоматически отслеживает следующие зависимости:

|Зависимости |Сведения|
|---------------|-------|
|HTTP/Https | Локальный или удаленный вызовы http/https |
|WCF-вызовов| Отслеживания только при использовании привязки на основе Http.|
|SQL | Вызовы, сделанные с помощью `SqlClient`. См. в разделе [это](##advanced-sql-tracking-to-get-full-sql-query) записи SQL выполните запрос.  |
|[Хранилище Azure (больших двоичных объектов, таблиц, очереди)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Вызовы, сделанные с помощью клиента хранилища Azure. |
|[Пакет SDK для клиента концентратора событий.](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Версии 1.1.0 и более поздних версий. |
|[Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Версии 3.0.0 и выше. |
|Azure Cosmos DB | Отслеживаются автоматически только, если используется HTTP/HTTPS. Режим TCP не будет отслеживаться с помощью Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Настройка автоматического слежения в консольных приложениях

Автоматически отслеживать зависимости от.NET/.NET Core консольных приложений, установите пакет Nuget `Microsoft.ApplicationInsights.DependencyCollector`и инициализировать `DependencyTrackingTelemetryModule` следующим образом:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Мониторинг работает автоматической зависимостей?

Зависимости автоматически собираются с помощью одного из следующих способов:

* С помощью инструментирования байт-кода вокруг методами выбора. (InstrumentationEngine из StatusMonitor или расширение Azure веб-приложения)
* Обратные вызовы EventSource
* Обратные вызовы DiagnosticSource (в последних пакетов SDK Core.NET/.NET)

## <a name="manually-tracking-dependencies"></a>Отслеживание зависимостей вручную

Ниже приведены некоторые примеры зависимостей, которые не автоматический сбор и поэтому требует отслеживания вручную.

* Azure Cosmos DB отслеживается автоматически только в том случае, если используется [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). Режим TCP не будет отслеживаться с помощью Application Insights.
* Redis

Эти зависимости, автоматически собираются пакетом SDK, вы можете отслеживать их вручную с помощью [TrackDependency API](api-custom-events-metrics.md#trackdependency) , используемое модулей сбора стандартной автоматически.

Например, формируя код на основе готовой сборки, можно назначить время для всех вызовов этого кода и таким образом выяснить, как он влияет на время отклика вашей системы. Чтобы эти данные отображались в диаграммах зависимостей в Application Insights, используйте для их отправки командлет `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Кроме того `TelemetryClient` предоставляет методы расширения `StartOperation` и `StopOperation` которого можно использовать для отслеживания зависимостей, вручную, как показано [здесь](custom-operations-tracking.md#outgoing-dependencies-tracking)

Если вы хотите отключить стандартный модуль отслеживания зависимостей, удалите ссылку на DependencyTrackingTelemetryModule в [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) для приложений ASP.NET. Для приложений ASP.NET Core, следуйте инструкциям [здесь](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Отслеживание вызовов AJAX с веб-страниц

Для веб-страниц, пакет SDK JavaScript Application Insights автоматически собирает вызовы AJAX как зависимости, указанные [здесь](javascript.md#ajax-performance). Этот документ посвящен зависимости из серверных компонентов.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Advanced отслеживания, чтобы получить полный SQL-запрос SQL

Для вызовов SQL, имя сервера и базы данных всегда собираются и хранятся в виде имя собранных `DependencyTelemetry`. Нет дополнительных поле с именем «данные», которая может содержать полный текст SQL-запроса.

Для приложений ASP.NET Core нет никакие дополнительные действия, необходимые, чтобы получить полный SQL-запрос.

Для приложений ASP.NET с помощью инструментирования байт-кода, который требует инструментирования ядра собираются полный SQL-запрос. Дополнительные действия для конкретных платформ, как описано ниже, являются обязательными.

| платформа | Шаги, которые необходимы для получения полный SQL-запрос |
| --- | --- |
| Веб-приложение Azure |В панели управления web app [откройте колонку Application Insights](../../azure-monitor/app/azure-web-apps.md) и позволяют выполнять команды SQL в .NET |
| Сервер IIS (виртуальная машина Azure в локальной среде и т. д.) | [Установите монитор состояний на сервере, где выполняется приложение](../../azure-monitor/app/monitor-performance-live-website-now.md) и перезапустите службы IIS.
| Облачная служба Azure |[Используйте задачу при запуске](../../azure-monitor/app/cloudservices.md) для [установите монитор состояния](monitor-performance-live-website-now.md#download) |
| IIS Express. | Не поддерживается

В указанных случаях правильным способом проверки двигателя инструментария является правильно установлена — путем проверки того, что версии пакета SDK собираются `DependencyTelemetry` является «rddp». «rdddsd» или «rddf» указывает зависимости собираются через обратные вызовы DiagnosticSource или EventSource и поэтому не будет отслеживаться полный SQL-запрос.

## <a name="where-to-find-dependency-data"></a>Где найти данные зависимостей

* [Схема приложения](app-map.md) наглядно представляет зависимости между приложением и смежными компонентами.
* [Диагностика транзакций](transaction-diagnostics.md) показано единой, коррелированных данных сервера.
* [Колонка "Браузеры"](javascript.md#ajax-performance) содержит вызовы AJAX из браузеров ваших пользователей.
* Щелкните по очереди медленные или неудачно завершенные запросы, чтобы проверить их вызовы зависимостей.
* [Аналитику](#analytics) можно использовать для запроса данных зависимостей.

## <a name="diagnosis"></a> Диагностика медленных запросов

Каждое событие запроса связано с вызовами зависимостей, исключениями и другими событиями, которые отслеживаются во время обработки запроса вашим приложением. Поэтому если некоторые запросы плохо, можно найти не является ли из-за замедление отклика от зависимости.

Давайте подробно рассмотрим подобный пример.

### <a name="tracing-from-requests-to-dependencies"></a>Трассировка от запросов до зависимостей

Откройте колонку "Производительность" и посмотрите на сетку запросов.

![Список запросов со средними значениями и их количеством](./media/asp-net-dependencies/02-reqs.png)

Верхний занимает много времени. Стоит попытаться выяснить, на что потрачено время.

Щелкните эту строку, чтобы просмотреть события для отдельного запроса.

![Список вхождений запросов](./media/asp-net-dependencies/03-instances.png)

Щелкните любой длительный экземпляр, чтобы проверить его более детально, и прокрутите вниз до удаленных вызовов зависимостей, связанных с этим запросом.

![Найдите вызовы удаленных зависимостей и определите необычную продолжительность.](./media/asp-net-dependencies/04-dependencies.png)

По-видимому, большую часть времени обработки этого запроса занял вызов локальной службы.

Выберите эту строку, чтобы получить дополнительную информацию.

![Щелкните эту удаленную зависимость, чтобы определить причину.](./media/asp-net-dependencies/05-detail.png)

Похоже, эта зависимость не на проблемные. Мы выявили проблему, так что теперь осталось выяснить, почему этот вызов занимает так много времени.

### <a name="request-timeline"></a>Временная шкала запроса

В другом случае вызов зависимости не имеет большую длительность. Но, переключившись на представление временной шкалы, мы видим, где произошла задержка во время внутренней обработки.

![Найдите вызовы удаленных зависимостей и определите необычную продолжительность.](./media/asp-net-dependencies/04-1.png)

Похоже, после первого вызова зависимости была большая задержка. Нам нужно изучить наш код, чтобы узнать, почему так произошло.

### <a name="profile-your-live-site"></a>Выполните профилирование активного сайта

Не имеете представления, куда девается время? [Профилировщик Application Insights](../../azure-monitor/app/profiler.md) трассировки HTTP вызывает для активного сайта и показаны функции кода, которые выполнялись дольше.

## <a name="failed-requests"></a>Failed requests (Неудачные запросы)

Неудачно завершенные запросы также могут быть связаны с неудачными вызовами зависимостей. Опять же, мы можем определить причину проблемы.

![Щелкните диаграмму неудачных запросов.](./media/asp-net-dependencies/06-fail.png)

Щелкните экземпляр неудачно завершенного запроса и просмотрите связанные с ним события.

![Щелкните тип запроса, а затем его экземпляр, чтобы открыть этот же экземпляр в другом представлении. Щелкните его еще раз, чтобы просмотреть подробную информацию об исключении.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Аналитика

Вы можете отслеживать зависимости, используя [язык запросов Kusto](/azure/kusto/query/). Ниже приведены некоторые примеры.

* Поиск неудачно завершенных вызовов зависимостей:

```

    dependencies | where success != "True" | take 10
```

* Поиск вызовов AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Поиск вызовов зависимостей, связанных с запросами:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Поиск вызовов AJAX, связанных с представлениями страницы:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Как выполняет автоматическое зависимостей сборщика отчетов неудачные вызовы зависимостей?*

* Вызовы зависимостей со сбоями будут иметь поле 'success', значение False. `DependencyTrackingTelemetryModule` не сообщает о `ExceptionTelemetry`. Описывается модель полного набора данных для зависимостей [здесь](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и каждый пакет SDK Application Insights модуль коллекции зависимостей также является открытым исходным кодом. Чтение и вносить дополнения в код или сообщить о проблемах в [официальный репозиторий GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>Дальнейшие действия

* [Исключения](../../azure-monitor/app/asp-net-exceptions.md)
* [Данные пользователей и страниц](../../azure-monitor/app/javascript.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
