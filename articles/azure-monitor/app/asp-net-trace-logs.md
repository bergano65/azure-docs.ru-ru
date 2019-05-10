---
title: Просмотр журналов трассировки .NET в Application Insights
description: Поиск журналов, созданных Trace, NLog или Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472052"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Просмотр журналов трассировки .NET или .Net Core в Application Insights

Отправка журналов диагностическую трассировку для приложения ASP.NET/ASP.NET Core из ILogger, NLog, log4Net или System.Diagnostics.Trace для [Azure Application Insights][start]. Вы можете затем поиска и просмотра. Эти журналы объединяются с другими файлами журнала из своего приложения, и позволяют определить трассировки, которые связаны с каждым запросом пользователя и сопоставлять их с другими событиями и отчеты об исключениях.

> [!NOTE]
> Требуется ли модуль записи журнала? Это полезный адаптер для сторонних средств ведения журнала. Но если вы еще не используете NLog, log4Net или System.Diagnostics.Trace, рассмотрите возможность вызова [ **Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) напрямую.
>
>
## <a name="install-logging-on-your-app"></a>Установка ведения журнала в приложении
Установите на выбранную платформу ведения журналов в проекте, что должно привести запись в файле app.config или web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Настройка Application Insights для сбора журналов
[Добавьте Application Insights в проект](../../azure-monitor/app/asp-net.md) Если вы еще не сделали это. Вы увидите параметр для включения сборщика журналов.

Или щелкните правой кнопкой мыши проект в обозревателе решений, чтобы **настроить Application Insights**. Выберите **настроить сбор трассировок** параметр.

> [!NOTE]
> Application Insights меню или журнал сборщика нельзя? Попробуйте выполнить [Устранение неполадок](#troubleshooting).

## <a name="manual-installation"></a>Ручная установка
Используйте этот метод, если ваш тип проекта не поддерживается программой установки Application Insights (например, если у вас проект настольного приложения Windows).

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект.
2. В обозревателе решений щелкните правой кнопкой мыши проект и выберите **управление пакетами NuGet**.
3. Поиск «Application Insights».
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

В пакет NuGet устанавливает необходимые сборки, а также изменяет web.config или app.config, если это возможно.

## <a name="ilogger"></a>ILogger

Примеры использования в реализации Application Insights ILogger с консольными приложениями и ASP.NET Core, см. в разделе [ApplicationInsightsLoggerProvider для .NET Core ILogger журналы](ilogger.md).

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
 * **Имя** задает имя собираемого события EventSource.
 * **Уровень** указывает уровень ведения журнала для сбора: *Критические*, *ошибка*, *Информационное*, *LogAlways*, *Verbose*, или *предупреждение*.
 * **Ключевые слова** (необязательно) укажите целочисленное значение используемых комбинаций ключевых слов.

## <a name="use-diagnosticsource-events"></a>Использование событий DiagnosticSource
Можно настроить отправку событий [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) в Application Insights в виде трассировок. Сначала установите пакет NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Затем внесите изменения в раздел «TelemetryModules» [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) файла.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Для каждого DiagnosticSource, который необходимо трассировать, добавьте запись с **имя** атрибуту присвоено имя DiagnosticSource.

## <a name="use-etw-events"></a>Использование событий трассировки событий Windows
Вы можете настроить отправку в Application Insights как трассировки событий трассировки событий для Windows (ETW). Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EtwCollector`. Затем внесите изменения в раздел «TelemetryModules» [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) файла.

> [!NOTE] 
> События трассировки событий Windows можно собирать только в том случае, если процесс, на котором размещается пакет SDK, запускается с удостоверением, которое является членом группы пользователей журнала производительности или Администраторы.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * **ProviderName** имя поставщика трассировки событий Windows для сбора.
 * **ProviderGuid** указывает идентификатор GUID поставщика трассировки событий Windows для сбора. Он может использоваться вместо `ProviderName`.
 * **Уровень** задает уровень ведения журнала для сбора. Это может быть *критическое*, *ошибка*, *Информационное*, *LogAlways*, *Verbose*, или *Предупреждение*.
 * **Ключевые слова** (необязательно) укажите целочисленное значение используемых комбинаций ключевых слов.

## <a name="use-the-trace-api-directly"></a>Напрямую использовать API трассировки
API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Например:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных,  например данных POST.

Уровень серьезности можно также добавить к сообщению. Как и для других данных телеметрии, вы можно добавлять значения свойств для фильтрации или поиска различных наборов трассировки. Например:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Это позволит легко отфильтровать в [поиска] [ diagnostic] все сообщения с определенной степенью серьезности, относящиеся к определенной базе данных.

## <a name="explore-your-logs"></a>Просмотр журналов
Запустите приложение в режиме отладки или разверните его.

В области "Обзор" вашего приложения в [на портале Application Insights][portal]выберите [поиска][diagnostic].

Можно, например:

* Фильтровать трассировки журнала или элементы с определенными свойствами.
* просматривать подробные сведения конкретного элемента;
* Поиск других системных данных журнала, к которому относится к тому же запросу пользователя (имеет то же значение OperationId).
* Сохраните конфигурацию страницы в список избранного.

> [!NOTE]
>Если приложение отправляет большие объемы данных, и вы используете пакет SDK Application Insights для ASP.NET версии 2.0.0-beta3 или более поздней версии, *Адаптивная выборка* функция может работать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="how-do-i-do-this-for-java"></a>Как это сделать в Java?
Используйте [адаптеры журналов Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>В контекстном меню проекта нет пункта для Application Insights 
* Убедитесь, что на компьютере разработки установлен Developer Analytics Tools. В Visual Studio **средства** > **расширения и обновления**, искать **Developer Analytics Tools**. Если это не так в **установленные** открытой вкладкой ", **Online** вкладку и установите его.
* Это может быть тип проекта, который не поддерживает Devloper средств аналитики. Используйте [установку вручную](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Нет возможности адаптера журнала в средстве настройки
* Сначала установите платформу ведения журналов.
* Если вы используете System.Diagnostics.Trace, убедитесь, что у вас есть его [в *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Убедитесь, что у вас есть последняя версия Application Insights. В Visual Studio, перейдите в раздел **средства** > **расширения и обновления**и откройте **обновления** вкладки. Если **Developer Analytics Tools** — существует, выберите его, чтобы обновить его.

### <a name="emptykey"></a>Получено сообщение об ошибке «ключ инструментирования не может быть пустым»
Возможно, вы установили пакет Nuget адаптера ведения журнала без установки Application Insights. В обозревателе решений щелкните правой кнопкой мыши *ApplicationInsights.config*и выберите **обновить Application Insights**. Вам будет предложено войти в Azure и создайте ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Отображаются трассировки, но не другие события в поиске по журналу диагностики
Может занять некоторое время для всех событий и запросы на получение по конвейеру.

### <a name="limits"></a>Какой объем данных сохраняется?
Объем сохраняемых данных влияют несколько факторов. Дополнительные сведения см. в разделе [ограничения](../../azure-monitor/app/api-custom-events-metrics.md#limits) части странице метрик событий пользователя.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Я не вижу некоторые записи журнала, которые я ожидал
Если ваше приложение отправляет большие объемы данных, и вы используете пакет SDK Application Insights для ASP.NET версии 2.0.0-beta3 или более поздней версии, функция адаптивной выборки может работать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Дальнейшие действия

* [Диагностика ошибок и исключений в ASP.NET][exceptions]
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