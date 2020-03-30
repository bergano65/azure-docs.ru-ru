---
title: Отслеживание зависимостей в Azure Application Insights | Документация Майкрософт
description: Мониторинг звонков зависимости из вашего собственного или веб-приложения Microsoft Azure с помощью Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1d4e8d1a0482257c92f47a00bd440e786c09c7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292124"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Отслеживание зависимостей в исследованиях приложений Azure 

*Зависимость* — это внешний компонент, который вызывается вашим приложением. Как правило, это служба, вызываемая с использованием HTTP, база данных или файловая система. [Application Insights](../../azure-monitor/app/app-insights-overview.md) измеряет продолжительность вызовов зависимости, независимо от того, не удается ли они или нет, а также дополнительную информацию, например название зависимости и так далее. Можно исследовать определенные вызовы зависимостей и соотносить их с запросами и исключениями.

## <a name="automatically-tracked-dependencies"></a>Автоматически отслеживаемые зависимости

Приложение Insights SDK для кораблей .NET и .NET Core, с `DependencyTrackingTelemetryModule` помощью которых является модуль телеметрии, который автоматически собирает зависимости. Эта коллекция зависимостей автоматически включается для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) и [ASP.NET основных](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) приложений, когда настроена в зависимости от связанных официальных документов. `DependencyTrackingTelemetryModule` поставляется в виде [этого](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) пакета NuGet, и привозится автоматически `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore`при использовании любого из пакетов NuGet или .

 `DependencyTrackingTelemetryModule`в настоящее время автоматически отслеживает следующие зависимости:

|Зависимости |Сведения|
|---------------|-------|
|Http/Https | Локальные или удаленные звонки http/https |
|WCF звонки| Только отслеживается автоматически, если http основе привязки используются.|
|SQL | Звонки, `SqlClient`сделанные с . Просмотрите [это](#advanced-sql-tracking-to-get-full-sql-query) для захвата запроса S'L.  |
|[Лазурное хранилище (Blob, Таблица, Очередь )](https://www.nuget.org/packages/WindowsAzure.Storage/) | Звонки, сделанные с клиентом хранения Azure. |
|[EventHub Клиент SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Версия 1.1.0 и выше. |
|[Пакет SDK для клиента служебной шины](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Версия 3.0.0 и выше. |
|Azure Cosmos DB | Используется только автоматически, если используется HTTP/HTTPS. Режим TCP не будет отслеживаться с помощью Application Insights. |

Если у вас отсутствует зависимость или используется другой SDK, убедитесь, что она в списке [автоматически собранных зависимостей.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) Если зависимость не собрана автоматически, можно отслеживать ее вручную с [помощью вызова зависимости трека.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Настройка автоматического отслеживания зависимостей в консольных приложениях

Чтобы автоматически отслеживать зависимости от консольных приложений .NET, установите пакет `Microsoft.ApplicationInsights.DependencyCollector`Nuget и инициализируют `DependencyTrackingTelemetryModule` следующим образом:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Для консольных приложений .NET Core TelemetryConfiguration.Active устарела. Ссылайтесь на рекомендации в [документации об обслуживании работников](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) и [документацию ASP.NET основных мониторинга](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Как работает автоматический мониторинг зависимостей?

Зависимости автоматически собираются с помощью одного из следующих методов:

* Использование приборов кода байта вокруг выбранных методов. (ИнструментацияEngine либо от StatusMonitor или расширение веб-приложений Azure)
* Обратные вызовы EventSource
* ДиагностическиеИсточник обратных вызовов (в последних SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Ручное отслеживание зависимостей

Ниже приведены некоторые примеры зависимостей, которые не собираются автоматически и, следовательно, требуют ручного отслеживания.

* Azure Cosmos DB отслеживается автоматически только в том случае, если используется [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). Режим TCP не будет отслеживаться с помощью Application Insights.
* Redis

Для тех зависимостей, которые автоматически не собираются SDK, их можно отслеживать вручную с помощью [API TrackDependency,](api-custom-events-metrics.md#trackdependency) который используется стандартными модулями автоматического сбора.

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

Кроме `TelemetryClient` того, предусмотрены `StartOperation` `StopOperation` методы расширения, которые могут быть использованы для ручного отслеживания зависимостей, как показано [здесь](custom-operations-tracking.md#outgoing-dependencies-tracking)

Если вы хотите отключить стандартный модуль отслеживания зависимостей, удалите ссылку на DependencyTrackingTelemetryModule в [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) для ASP.NET приложений. Для ASP.NET основных приложений, следуйте инструкциям [здесь](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Отслеживание звонков AJAX с веб-страниц

Для веб-страниц Application Insights JavaScript SDK автоматически собирает вызовы AJAX в качестве зависимостей.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Расширенный слежения за СЗЛ, чтобы получить полный си-L запрос

Для вызовов S'L имя сервера и базы данных всегда `DependencyTelemetry`собирается и хранится в качестве имени собранного. Есть дополнительное поле под названием «данные», которое может содержать полный текст запроса S'L.

Для ASP.NET приложений Core нет никаких дополнительных шагов, необходимых для получения полного запроса S'L.

Для ASP.NET приложений полный запрос S'L собирается с помощью приборов кода байта, который требует приборного двигателя. Требуются дополнительные шаги, связанные с платформой, как описано ниже.

| Платформа | Шаг (ы) Необходимо получить полный запрос S'L |
| --- | --- |
| Веб-приложение Azure |В панели управления веб-приложениями [откройте лезвие Application Insights](../../azure-monitor/app/azure-web-apps.md) и включите команды S'L под .NET |
| IIS Server (Azure VM, on-prem и так далее.) | Используйте модуль Status Monitor PowerShell для [установки двигателя приборов](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) и перезапуска IIS. |
| Облачная служба Azure | Добавление [задачи запуска для установки StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Ваше приложение должно быть на борту ApplicationInsights SDK во время сборки, установив пакеты NuGet для [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) или [ASP.NET основных приложений](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express. | Не поддерживается

В вышеуказанных случаях правильным способом проверки правильного установки приборного двигателя является подтверждение того, `DependencyTelemetry` что версия SDK собранной является 'rddp'. 'rdddsd' или 'rddf' указывает на то, что зависимости собираются через обратные вызовы DiagnosticSource или EventSource, и, следовательно, полный запрос S'L не будет захвачен.

## <a name="where-to-find-dependency-data"></a>Где найти данные зависимостей

* [Схема приложения](app-map.md) наглядно представляет зависимости между приложением и смежными компонентами.
* [Диагностика транзакций](transaction-diagnostics.md) показывает унифицированные, коррелированные данные сервера.
* [Вкладка браузеров](javascript.md) показывает звонки AJAX из браузеров пользователей.
* Щелкните по очереди медленные или неудачно завершенные запросы, чтобы проверить их вызовы зависимостей.
* [Аналитику](#logs-analytics) можно использовать для запроса данных зависимостей.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Диагностика медленных запросов

Каждое событие запроса связано с вызовами зависимости, исключениями и другими событиями, которые отслеживаются во время обработки запроса. Так что, если некоторые запросы делают плохо, вы можете узнать, является ли это из-за медленной реакции от зависимости.

### <a name="tracing-from-requests-to-dependencies"></a>Трассировка от запросов до зависимостей

Откройте вкладку **«Производительность»** и перейдите к вкладке **«Зависимости»** в верхней части рядом с операциями.

Нажмите на имя зависимости в общем **зачете.** После выбора зависимости справа будет отображаться график распределения продолжительностей зависимости.

![В вкладке производительности нажмите на вкладку Зависимости в верхней части, а затем имя зависимости на диаграмме](./media/asp-net-dependencies/2-perf-dependencies.png)

Нажмите на кнопку "Синие **образцы"** в правом нижнем углу, а затем на примере, чтобы увидеть детали сквозной транзакции.

![Нажмите на пример, чтобы увидеть детали сквозной транзакции](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Выполните профилирование активного сайта

Не имеете представления, куда девается время? [Профайлер Application Insights](../../azure-monitor/app/profiler.md) отслеживает http звонки на ваш живой сайт и показывает функции в коде, которые заняли больше времени.

## <a name="failed-requests"></a>Failed requests (Неудачные запросы)

Неудачно завершенные запросы также могут быть связаны с неудачными вызовами зависимостей.

Мы можем перейти к вкладке **Сбои** слева, а затем нажмите на вкладку **зависимостей** в верхней части.

![Щелкните диаграмму неудачных запросов.](./media/asp-net-dependencies/4-fail.png)

Здесь вы сможете увидеть неудавшийся подсчет зависимостей. Чтобы получить более подробную информацию о неудачном происшествии, пытаясь нажать на имя зависимости в нижней таблице. Вы можете нажать на кнопку "Синие **зависимости"** в правом нижнем углу, чтобы получить сквозную информацию о транзакциях.

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Как автоматический сборщик зависимостей сообщает о неудачных вызовах зависимостей?*

* Неудачные вызовы зависимости будут иметь поле «успешного» для ложного. `DependencyTrackingTelemetryModule`не сообщает `ExceptionTelemetry`. Полная модель данных для зависимости описана [здесь](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
Как и все приложения Исследования SDK, модуль сбора зависимостей также с открытым исходным кодом. Прочитайте и внесите свой вклад в код или сообщите о проблемах на [официальном репо GitHub.](https://github.com/Microsoft/ApplicationInsights-dotnet-server)

## <a name="next-steps"></a>Дальнейшие действия

* [Исключения](../../azure-monitor/app/asp-net-exceptions.md)
* [Данные пользователей и страниц](../../azure-monitor/app/javascript.md)
* [Доступность](../../azure-monitor/app/monitor-web-app-availability.md)
