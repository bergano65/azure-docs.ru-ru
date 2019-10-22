---
title: Просмотр журналов трассировки .NET в Application Insights
description: Поиск журналов, созданных трассировкой, NLog или Log4Net.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: 352e31e2a2f1a88a33e82134460e6df0911dbd2e
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677632"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Изучение журналов трассировки .NET и .NET Core и Python в Application Insights

Отправка журналов трассировки диагностики для приложения ASP.NET/ASP.NET Core из ILogger, NLog, log4Net или System. Diagnostics. Trace в [Azure Application Insights][start]. Для приложений Python отправьте журналы трассировки диагностики с помощью Азурелогхандлер в Опенценсус Python для Azure Monitor. После этого можно просматривать и искать их. Эти журналы объединяются с другими файлами журналов из приложения, поэтому можно выявление трассировок, связанных с каждым запросом пользователя, и их сопоставление с другими событиями и отчетами об исключениях.

> [!NOTE]
> Нужен ли модуль записи журнала? Это полезный адаптер для средств ведения журнала сторонних поставщиков. Но если вы еще не используете NLog, log4Net или System. Diagnostics. Trace, попробуйте просто вызвать [**Application Insights TrackTrace ()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) напрямую.
>
>
## <a name="install-logging-on-your-app"></a>Установка ведения журнала в приложении
Установите выбранную платформу ведения журнала в проект, что приведет к созданию записи в файле App. config или Web. config.

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
[Добавьте Application Insights в проект](../../azure-monitor/app/asp-net.md) , если вы еще этого не сделали. Вы увидите параметр для включения сборщика журналов.

Или щелкните правой кнопкой мыши проект в обозреватель решений, чтобы **настроить Application Insights**. Выберите параметр **Настройка сбора трассировки** .

> [!NOTE]
> Нет Application Insights меню или сборщика журналов? Попробуйте выполнить [Устранение неполадок](#troubleshooting).

## <a name="manual-installation"></a>Ручная установка
Используйте этот метод, если ваш тип проекта не поддерживается программой установки Application Insights (например, если у вас проект настольного приложения Windows).

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект.
2. В обозреватель решений щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.
3. Выполните поиск по запросу «Application Insights».
4. Выберите один из следующих пакетов.

   - Для ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
[ ![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Для NLog: [Microsoft. ApplicationInsights. нлогтаржет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
[ ![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Для Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
[ ![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Для System. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
[ ![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [@No__t_3NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [Microsoft. ApplicationInsights. диагностиксаурцелистенер](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [@No__t_3NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [Microsoft. ApplicationInsights. етвколлектор](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Пакет NuGet устанавливает необходимые сборки и изменяет файл Web. config или App. config, если это применимо.

## <a name="ilogger"></a>ILogger

Примеры использования реализации Application Insights ILogger с консольными приложениями и ASP.NET Core см. в разделе [журналы ILogger для .NET Core аппликатионинсигхтслогжерпровидер](ilogger.md).

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
 * **Имя** указывает имя объекта EventSource, который необходимо получить.
 * **Уровень** задает уровень ведения журнала для сбора: *критическая*, *Ошибка*, *информационное*, *логалвайс*, *подробное*или *предупреждение*.
 * **Ключевые слова** (необязательно) укажите целочисленное значение сочетаний ключевых слов для использования.

## <a name="use-diagnosticsource-events"></a>Использование событий DiagnosticSource
Можно настроить отправку событий [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) в Application Insights в виде трассировок. Сначала установите пакет NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Затем измените раздел "Телеметримодулес" файла [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Для каждого DiagnosticSource, который необходимо отследить, добавьте запись с атрибутом **Name** , для которой задано имя DiagnosticSource.

## <a name="use-etw-events"></a>Использование событий ETW
Можно настроить отправку событий трассировки событий Windows (ETW) в Application Insights как трассировки. Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EtwCollector`. Затем измените раздел "Телеметримодулес" файла [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> События ETW можно собирать, только если процесс, в котором размещается пакет SDK, выполняется с удостоверением, который является членом журналов производительности или администраторов.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * **ProviderName** — это имя поставщика ETW, который необходимо получить.
 * **Провидергуид** указывает идентификатор GUID поставщика трассировки событий Windows, который необходимо получить. Его можно использовать вместо `ProviderName`.
 * **Уровень** задает уровень ведения журнала для сбора. Это может быть *критическое*, *Ошибка*, *информационное*, *логалвайс*, *подробное*или *предупреждение*.
 * **Ключевые слова** (необязательно) задайте целочисленное значение сочетаний ключевых слов для использования.

## <a name="use-the-trace-api-directly"></a>Использование API трассировки напрямую
API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Пример.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных, например данных POST.

Можно также добавить уровень серьезности к сообщению. И, как и другие данные телеметрии, можно добавить значения свойств, чтобы помочь фильтровать или искать различные наборы трассировок. Пример.

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Это позволит легко отфильтровать [Поиск][diagnostic] всех сообщений определенного уровня серьезности, относящихся к определенной базе данных.

## <a name="azureloghandler-for-opencensus-python"></a>Азурелогхандлер для Опенценсус Python
Обработчик журнала Azure Monitor позволяет экспортировать журналы Python в Azure Monitor.

Выполните инструментирование приложения с помощью [пакета SDK для Опенценсус Python](../../azure-monitor/app/opencensus-python.md) для Azure Monitor.

В этом примере показано, как отправить журнал уровня предупреждения в Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Просмотр журналов
Запустите приложение в режиме отладки или разверните его в реальном времени.

В области Обзор приложения на [портале Application Insights][portal]выберите [Поиск][diagnostic].

Можно, например:

* Фильтрация по трассировке журнала или к элементам с конкретными свойствами.
* просматривать подробные сведения конкретного элемента;
* Поиск других данных системного журнала, связанных с одним и тем же запросом пользователя.
* Сохранение конфигурации страницы в избранном.

> [!NOTE]
>Если приложение отправляет большой объем данных и вы используете Application Insights пакет SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция *адаптивной выборки* может выполнять работу и отправлять только часть вашей телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Устранение неисправностей
### <a name="how-do-i-do-this-for-java"></a>Как это сделать в Java?
Используйте [адаптеры журналов Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>В контекстном меню проекта нет пункта для Application Insights
* Убедитесь, что на компьютере разработки установлен Developer Analytics Tools. В Visual Studio **tools** > **расширения и обновления**найдите **Developer Analytics Tools**. Если она отсутствует на вкладке **установлен** , откройте вкладку в **сети** и установите ее.
* Это может быть тип проекта, который не поддерживает средства аналитики Девлопер. Используйте [установку вручную](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>В средстве настройки отсутствует параметр "адаптер журнала"
* Сначала установите платформу ведения журнала.
* Если вы используете System. Diagnostics. Trace, убедитесь, что он [настроен в *файле Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Убедитесь, что у вас установлена последняя версия Application Insights. В Visual Studio последовательно выберите **инструменты** > **расширения и обновления**, а затем откройте вкладку **обновления** . Если **Developer Analytics Tools** , выберите его, чтобы обновить.

### <a name="emptykey"></a>Я получаю сообщение об ошибке "ключ инструментирования не может быть пустым"
Возможно, вы установили пакет NuGet для адаптера ведения журнала без установки Application Insights. В обозреватель решений щелкните правой кнопкой мыши *файл ApplicationInsights. config*и выберите **Update Application Insights (обновить**). Вам будет предложено войти в Azure и создать Application Insights ресурс или повторно использовать существующий. Это устранит проблему.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Я могу видеть трассировки, но не другие события в поиске по журналу диагностики
Получение всех событий и запросов через конвейер может занять некоторое время.

### <a name="limits"></a>Какой объем данных сохраняется?
На объем хранящихся данных влияют несколько факторов. Дополнительные сведения см. в разделе [ограничения](../../azure-monitor/app/api-custom-events-metrics.md#limits) страницы метрики событий клиента.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Я не вижу некоторые ожидаемые записи журнала
Если приложение отправляет волуминаус объемы данных и вы используете пакет SDK Application Insights для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может выполнять работу и отправлять только часть телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Дальнейшие действия

* [Диагностика сбоев и исключений в ASP.NET][exceptions]
* [Дополнительные сведения о поиске][diagnostic]
* [Наблюдение за доступностью и скоростью реагирования веб-сайта][availability]
* [Устранение неполадок][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md