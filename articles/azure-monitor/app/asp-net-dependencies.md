---
title: Отслеживание зависимостей в Azure Application Insights | Документация Майкрософт
description: Отслеживайте вызовы зависимостей из локального или Microsoft Azure веб-приложения с помощью Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/25/2019
ms.openlocfilehash: 7b23da81143a4ae66d9f25cd953c4a3952f27455
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678366"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Отслеживание зависимостей в Azure Application Insights 

*Зависимость* – это внешний компонент, который вызывается приложением. Как правило, это служба, вызываемая с использованием HTTP, база данных или файловая система. [Application Insights](../../azure-monitor/app/app-insights-overview.md) измеряет продолжительность вызовов зависимостей, вне зависимости от того, произошел ли сбой, а также дополнительные сведения, такие как имя зависимости и т. д. Можно исследовать конкретные вызовы зависимостей и сопоставлять их с запросами и исключениями.

## <a name="automatically-tracked-dependencies"></a>Автоматически отслеживание зависимостей

Application Insights пакеты SDK для .NET и .NET Core входят в состав `DependencyTrackingTelemetryModule`, который является модулем телеметрии, автоматически собирающим зависимости. Эта коллекция зависимостей включается автоматически для приложений [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) , если они настроены в соответствии со связанными официальными документами.  `DependencyTrackingTelemetryModule` поставляется в качестве [этого](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) пакета NuGet и автоматически передается при использовании любого из пакетов NuGet `Microsoft.ApplicationInsights.Web` или `Microsoft.ApplicationInsights.AspNetCore`.

 `DependencyTrackingTelemetryModule` в настоящее время отслеживает следующие зависимости автоматически:

|Зависимости |Сведения|
|---------------|-------|
|HTTP/HTTPS | Локальные или удаленные вызовы HTTP/HTTPS |
|Вызовы WCF| Отслеживание выполняется автоматически только при использовании привязок на основе HTTP.|
|SQL | Вызовы, выполняемые с `SqlClient`. См. [этот](#advanced-sql-tracking-to-get-full-sql-query) раздел для записи SQL.  |
|[Хранилище Azure (большой двоичный объект, таблица, очередь)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Вызовы, сделанные с помощью клиента службы хранилища Azure. |
|[Пакет SDK клиента EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Версия 1.1.0 и выше. |
|[Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Версия 3.0.0 и выше. |
|Azure Cosmos DB; | Отслеживание производится автоматически, только если используется HTTP/HTTPS. Режим TCP не будет отслеживаться с помощью Application Insights. |

Если отсутствует зависимость или используется другой пакет SDK, убедитесь, что он находится в списке [автособираемых зависимостей](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Если зависимость не собираются автоматически, вы по-прежнему можете отслеживанить ее вручную с помощью [вызова зависимости Track](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Настройка автоматического отслеживания зависимостей в консольных приложениях

Чтобы автоматически отслеживаниь зависимостей из консольных приложений .NET или .NET Core, установите пакет NuGet `Microsoft.ApplicationInsights.DependencyCollector` и инициализируйте `DependencyTrackingTelemetryModule` следующим образом.

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Как работает автоматическое отслеживание зависимостей?

Зависимости автоматически собираются с помощью одного из следующих методов:

* Использование инструментирования кода на основе методов Select. (Инструментатионенгине из Статусмонитор или расширения веб-приложения Azure)
* Обратные вызовы EventSource
* Обратные вызовы DiagnosticSource (в последних пакетах SDK для .NET и .NET Core)

## <a name="manually-tracking-dependencies"></a>Отслеживание зависимостей вручную

Ниже приведены некоторые примеры зависимостей, которые не собираются автоматически, и поэтому требуется отслеживание вручную.

* Azure Cosmos DB отслеживается автоматически только в том случае, если используется [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). Режим TCP не будет отслеживаться с помощью Application Insights.
* Redis

Для этих зависимостей, не собираемых пакетом SDK автоматически, их можно отформатировать вручную с помощью [API TrackDependency](api-custom-events-metrics.md#trackdependency) , который используется стандартными модулями автоматической коллекции.

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

Кроме того, `TelemetryClient` предоставляет методы расширения `StartOperation` и `StopOperation`, которые можно использовать для трассировки зависимостей вручную, как показано [ниже](custom-operations-tracking.md#outgoing-dependencies-tracking) .

Если вы хотите отключить стандартный модуль отслеживания зависимостей, удалите ссылку на Депенденцитраккингтелеметримодуле в [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) для приложений ASP.NET. Для ASP.NET Core приложений следуйте указаниям [здесь](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Отслеживание вызовов AJAX с веб-страниц

Для веб-страниц Application Insights пакет SDK для JavaScript автоматически собирает вызовы AJAX как зависимости.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Расширенное отслеживание SQL для получения полного SQL запроса

Для вызовов SQL имя сервера и базы данных всегда собирается и сохраняется как имя собранного `DependencyTelemetry`. Имеется дополнительное поле с именем "Data", которое может содержать полный текст запроса SQL.

Для ASP.NET Core приложений никаких дополнительных действий по получению полного SQL Server не требуется.

Для приложений ASP.NET полный запрос SQL собираются с помощью инструментирования кода в байтах, для которого требуется модуль инструментирования. Дополнительные действия, связанные с платформой, как описано ниже, являются обязательными.

| платформа | Шаг (ы), необходимый для получения полного SQL-запроса |
| --- | --- |
| Веб-приложение Azure |На панели управления веб-приложения [откройте колонку Application Insights](../../azure-monitor/app/azure-web-apps.md) и включите команды SQL в разделе .NET. |
| Сервер IIS (виртуальная машина Azure, локальная сеть и т. д.) | Используйте модуль PowerShell монитор состояния для [установки модуля инструментирования](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) и перезапуска IIS. |
| Облачная служба Azure | Добавление [задачи запуска для установки статусмонитор](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Приложение следует подключить к пакету SDK для ApplicationInsights во время сборки, установив пакеты NuGet для приложений [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) или [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express. | Не поддерживается

В приведенных выше случаях правильный способ проверки правильности установки модуля инструментирования — проверка того, что версия пакета SDK для сбора `DependencyTelemetry` — "рддп". "рдддсд" или "рддф" указывает, что зависимости собираются через обратные вызовы DiagnosticSource или EventSource, поэтому полный запрос SQL не будет записан.

## <a name="where-to-find-dependency-data"></a>Где найти данные зависимостей

* [Схема приложения](app-map.md) наглядно представляет зависимости между приложением и смежными компонентами.
* [Диагностика транзакций](transaction-diagnostics.md) отображает унифицированные коррелированные данные сервера.
* На [вкладке браузеры](javascript.md) ОТОБРАЖАЮТСЯ вызовы AJAX из браузеров пользователей.
* Щелкните по очереди медленные или неудачно завершенные запросы, чтобы проверить их вызовы зависимостей.
* [Аналитику](#logs-analytics) можно использовать для запроса данных зависимостей.

## <a name="diagnosis"></a> Диагностика медленных запросов

Каждое событие запроса связано с вызовами зависимостей, исключениями и другими событиями, которые отправляются во время обработки запроса приложением. Поэтому, если некоторые запросы выполняют неплохое действие, можно выяснить, не является ли это причиной из-за медленных ответов от зависимости.

### <a name="tracing-from-requests-to-dependencies"></a>Трассировка от запросов до зависимостей

Откройте вкладку " **производительность** " и перейдите на вкладку " **зависимости** " в верхней части рядом с пунктом "операции".

В разделе Общие щелкните **имя зависимости** . После выбора зависимости диаграмма распределения длительности для этой зависимости будет отображаться справа.

![На вкладке "производительность" щелкните вкладку зависимости вверху, а затем имя зависимости в диаграмме.](./media/asp-net-dependencies/2-perf-dependencies.png)

Нажмите **синюю кнопку** в правом нижнем углу, а затем в образце, чтобы просмотреть сведения о сквозной транзакции.

![Щелкните образец, чтобы просмотреть сведения о сквозной транзакции.](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Выполните профилирование активного сайта

Не имеете представления, куда девается время? [Application Insights Profiler](../../azure-monitor/app/profiler.md) ОТСЛЕЖИВАЕТ вызовы HTTP на ваш активный сайт и показывает функции в коде, которые заняли самое длинное время.

## <a name="failed-requests"></a>Failed requests (Неудачные запросы)

Неудачно завершенные запросы также могут быть связаны с неудачными вызовами зависимостей.

Можно открыть вкладку " **ошибки** " слева, а затем щелкнуть вкладку **зависимости** вверху.

![Щелкните диаграмму неудачных запросов.](./media/asp-net-dependencies/4-fail.png)

Здесь вы увидите число неудачных зависимостей. Чтобы получить дополнительные сведения о неудачном обнаруженном экземпляре, попробуйте щелкнуть имя зависимости в нижней таблице. Чтобы получить сведения о сквозной транзакции, щелкните синюю кнопку " **зависимости** " в правом нижнем углу.

## <a name="logs-analytics"></a>Журналы (Analytics)

Вы можете отслеживать зависимости, используя [язык запросов Kusto](/azure/kusto/query/). Ниже приведены некоторые примеры.

* Поиск неудачно завершенных вызовов зависимостей:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Поиск вызовов AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Поиск вызовов зависимостей, связанных с запросами:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Поиск вызовов AJAX, связанных с представлениями страницы:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Как в отчете автоматического сборщика зависимостей не удалось вызвать зависимости?*

* Неудачные вызовы зависимостей будут иметь поле Success со значением false. `DependencyTrackingTelemetryModule` не сообщает `ExceptionTelemetry`. Полная модель данных для зависимости описана [здесь](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и каждый Application Insights пакет SDK, модуль коллекции зависимостей также является открытым исходным кодом. Прочтите код и догляните в него или сообщите о проблемах в [официальном репозитории GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Дальнейшие действия

* [Исключения](../../azure-monitor/app/asp-net-exceptions.md)
* [Данные пользователей и страниц](../../azure-monitor/app/javascript.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
