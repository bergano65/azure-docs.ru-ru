---
title: Просмотр журналов трассировки .NET в Application Insights
description: Поиск журналов, генерируемых Trace, NLog или Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276274"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Исследуйте журналы трассировки .NET/.NET Core и Python в application Insights

Отправить журналы отслеживания диагностики для вашего ASP.NET/ASP.NET основного приложения от ILogger, NLog, log4Net или System.Diagnostics.Trace to [Azure Application Insights.][start] Для приложений Python отправьте журналы отслеживания диагностики с помощью AzureLogHandler в OpenCensus Python для Azure Monitor. Затем вы можете исследовать и искать их. Эти журналы объединяются с другими файлами журнала из приложения, так что вы можете идентифицировать следы, связанные с каждым запросом пользователя, и соотнести их с другими событиями и отчетами об исключениях.

> [!NOTE]
> Вам нужен модуль захвата журнала? Это полезный адаптер для сторонних лесозаготовителей. Но если вы еще не используете NLog, log4Net или System.Diagnostics.Trace, подумайте только о том, чтобы позвонить в [**Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) напрямую.
>
>
## <a name="install-logging-on-your-app"></a>Установка ведения журнала в приложении
Установите выбранную платформу регистрации в проекте, что должно привести к записи в app.config или web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Настройка Application Insights для сбора журналов
[Добавьте Application Insights в свой проект](../../azure-monitor/app/asp-net.md), если вы еще этого не сделали. Вы увидите параметр для включения сборщика журналов.

Или нажмите правой кнопкой мыши вашего проекта в Solution Explorer, чтобы **настроить анализ приложений.** Выберите вариант **сбора контуров.**

> [!NOTE]
> Вы не видите меню Application Insights или параметр для включения сборщика журналов? Попробуйте выполнить [Устранение неполадок](#troubleshooting).

## <a name="manual-installation"></a>Ручная установка
Используйте этот метод, если ваш тип проекта не поддерживается программой установки Application Insights (например, если у вас проект настольного приложения Windows).

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект.
2. В Solution Explorer щелкните по праву на ваш проект и выберите **пакеты Управления NuGet.**
3. Поиск "Исследования приложений".
4. Выберите один из следующих пакетов.

   - Для ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Для NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Для Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Для System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Пакет NuGet устанавливает необходимые сборки и изменяет web.config или app.config, если это применимо.

## <a name="ilogger"></a>ILogger

Примеры использования реализации Application Insights ILogger с помощью консольных приложений и ASP.NET Core [см.](ilogger.md)

## <a name="insert-diagnostic-log-calls"></a>Вставка вызовов журнала диагностики
При использовании System.Diagnostics.Trace типичный вызов будет выглядеть следующим образом:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Если вы предпочитаете log4net или NLog, используйте:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Использование событий EventSource
Можно настроить отправку событий [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) в Application Insights в виде трассировок. Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Затем измените раздел `TelemetryModules` файла [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * **Имя** указывает название EventSource для сбора.
 * **Уровень** определяет уровень регистрации для сбора: *Критические*, *Ошибка*, *Информационный*, *LogAlways*, *Verbose*, или *Предупреждение*.
 * **Ключевые слова** (необязательно) указывают величину комбинаций ключевых слов для использования.

## <a name="use-diagnosticsource-events"></a>Используйте события DiagnosticSource
Можно настроить отправку событий [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) в Application Insights в виде трассировок. Во-первых, [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) установите пакет NuGet. Затем отодвите раздел "ТелеметрияМодики" [файла ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Для каждого DiagnosticSource, который вы хотите отследить, добавьте запись с набором атрибута **имени** к названию вашего DiagnosticSource.

## <a name="use-etw-events"></a>Использование событий ETW
Вы можете настроить отслеживание событий для событий Windows (ETW), которые будут отправлены в Application Insights в качестве следов. Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EtwCollector`. Затем отодвите раздел "ТелеметрияМодики" [файла ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> События ETW могут быть собраны только в том случае, если процесс, в котором размещается SDK, выполняется под удостоверением, являющимся пользователем или администратором журнала производительности.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * **ProviderName** — это имя поставщика ETW для сбора.
 * **ProviderGuid** определяет GUID поставщика ETW для сбора. Он может быть `ProviderName`использован вместо .
 * **Уровень** устанавливает уровень регистрации для сбора. Это может быть *критический*, *Ошибка*, *Информационный*, *LogAlways*, *Verbose*, или *Предупреждение*.
 * **Ключевые слова** (необязательно) устанавливают значение комплексных комбинаций ключевых слов для использования.

## <a name="use-the-trace-api-directly"></a>Используйте API трассировки напрямую
API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Пример:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных,  например данных POST.

Вы также можете добавить уровень серьезности в ваше сообщение. И, как и другие телеметрии, вы можете добавить значения свойств, чтобы помочь фильтровать или искать различные наборы следов. Пример:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Это позволит легко отфильтровать в [поиске][diagnostic] все сообщения определенного уровня серьезности, относящиеся к конкретной базе данных.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler для OpenCensus Python
Обработчик журнала Azure Monitor позволяет экспортировать журналы Python в Azure Monitor.

Инструмент приложения с [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) для Azure Monitor.

В этом примере показано, как отправить журнал уровня предупреждения в журнал Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Просмотр журналов
Запустите приложение в режиме отладки или развернуть его в режиме живого.

В обзорной панели приложения [на портале Application Insights][portal]выберите [поиск.][diagnostic]

Можно, например:

* Фильтр на следах журнала или на элементах с определенными свойствами.
* просматривать подробные сведения конкретного элемента;
* Найдите другие данные системного журнала, которые относятся к тому же запросу пользователя (имеет тот же OperationId).
* Сохранить конфигурацию страницы в качестве избранного.

> [!NOTE]
>Если ваше приложение отправляет много данных, и вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или позже, функция *адаптивной выборки* может работать и отправлять только часть телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="how-do-i-do-this-for-java"></a>Как это сделать в Java?
Используйте [адаптеры журналов Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>В контекстном меню проекта нет пункта для Application Insights 
* Убедитесь, что на машине разработки установлены инструменты для аналитики разработчиков. В Visual Studio **Инструменты** > **расширения и обновления**, ищите разработчик Analytics **Tools**. Если его нет на **вкладке "Установлено",** откройте вкладку **Online** и установите ее.
* Это может быть тип проекта, который Devloper Analytics Tools не поддерживает. Используйте [установку вручную](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>В инструменте настройки нет опции адаптера для журналов
* Сначала установите платформу журналов.
* Если вы используете System.Diagnostics.Trace, убедитесь, что у вас есть он [настроен в *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Убедитесь, что у вас есть последняя версия Приложения Исследования. В Visual Studio перейдите на расширения и обновления **инструментов** > **Extensions and Updates**и откройте вкладку **«Обновления».** Если **есть инструменты аналитики разработчика,** выберите его для его обновления.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Я получаю сообщение об ошибке "Инструментальный ключ не может быть пустым"
Возможно, вы установили пакет адаптера nuget для регистрации без установки Application Insights. В Solution Explorer, правой кнопкой *мыши ApplicationInsights.config*, и выберите **Обновление Приложения Исследования**. Вам будет предложено войти в Azure и создать ресурс Application Insights или повторно использовать существующий ресурс. Это должно решить проблему.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Я вижу следы, но не другие события в диагностическом поиске
Это может занять некоторое время для всех событий и запросов, чтобы пройти через конвейер.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Какой объем данных сохраняется?
На объем сохраненных данных влияет несколько факторов. Для получения дополнительной [информации](../../azure-monitor/app/api-custom-events-metrics.md#limits) смотрите раздел лимитов на странице метрик события клиента.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Я не вижу некоторые записи журнала, что я ожидал
Если ваше приложение отправляет объемные объемы данных и вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или позже, функция адаптивной выборки может работать и отправлять только часть телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Дальнейшие действия

* [Диагностика ошибок и исключений в ASP.NET][exceptions]
* [Подробнее о поиске][diagnostic]
* [Наблюдение за доступностью и скоростью реагирования веб-сайта][availability]
* [Устранение неполадок][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
