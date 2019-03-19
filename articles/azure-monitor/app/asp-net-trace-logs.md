---
title: Просмотр журналов трассировки .NET в Application Insights
description: Поиск журналов, созданных с помощью Trace, Log4Net или NLog.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552152"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Просмотр журналов трассировки .NET или .Net Core в Application Insights

Если вы используете ILogger, NLog, log4Net или System.Diagnostics.Trace для диагностической трассировки в приложении ASP.NET или ASP.NET Core, можете передавать журналы трассировки в [Azure Application Insights][start] для поиска и просмотра. Журналы будут объединены с другими данными телеметрии, поступающими из вашего приложения, давая возможность определять трассировки, связанные с обработкой каждого запроса пользователя, и сопоставлять их с другими событиями и отчетами об исключениях.

> [!NOTE]
> Требуется модуль записи журнала? Это полезный адаптер для сторонних средств ведения журнала, но если вы еще не используете NLog, log4Net или System.Diagnostics.Trace, рассмотрите возможность вызова [Application Insights TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) напрямую.
>
>

## <a name="install-logging-on-your-app"></a>Установка ведения журнала в приложении
Установите выбранную платформу ведения журналов в своем проекте. При этом должна появиться запись в файлах app.config или web.config.

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
**[Добавьте Application Insights в свой проект](../../azure-monitor/app/asp-net.md)**, если вы еще этого не сделали. Вы увидите параметр для включения сборщика журналов.

Или выберите **Настроить Application Insights** , щелкнув правой кнопкой мыши на своем проекте в обозревателе решений. Выберите параметр **Настроить сбор трассировок**.

*Вы не видите меню Application Insights или параметр для включения сборщика журналов?* Попробуйте выполнить [Устранение неполадок](#troubleshooting).

## <a name="manual-installation"></a>Ручная установка
Используйте этот метод, если ваш тип проекта не поддерживается программой установки Application Insights (например, если у вас проект настольного приложения Windows).

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект.
2. В обозревателе решений щелкните правой кнопкой мыши ваш проект и выберите **Управление пакетами NuGet**.
3. Поиск Application Insights
4. Выберите один из следующих пакетов.

   - Для ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Для NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Для Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Для System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Пакет NuGet устанавливает необходимые сборки, а также вносит изменения в файл web.config или app.config (если применимо).

## <a name="ilogger"></a>ILogger

Примеры использования реализации ILogger для Application Insights с ASP.NET Core и консольными приложениями см. в этой [статье](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Вставка вызовов журнала диагностики
При использовании System.Diagnostics.Trace типичный вызов будет выглядеть следующим образом:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Если вы предпочитаете log4net или NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Использование событий EventSource
Можно настроить отправку событий [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) в Application Insights в виде трассировок. Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Затем измените раздел `TelemetryModules` файла [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * `Name` задает имя собираемого события EventSource.
 * `Level` задает уровень ведения журнала для сбора. Возможные значения: `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (необязательный) задает целочисленное значение используемых комбинаций ключевых слов.

## <a name="using-diagnosticsource-events"></a>Использование событий DiagnosticSource
Можно настроить отправку событий [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) в Application Insights в виде трассировок. Сначала установите пакет NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Затем измените раздел `TelemetryModules` файла [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Для каждого объекта DiagnosticSource, трассировку которого необходимо выполнять, добавьте запись с атрибутом `Name`, которому присвоено имя DiagnosticSource.

## <a name="using-etw-events"></a>Использование событий трассировки событий Windows
Можно настроить отправку событий трассировки событий Windows в Application Insights в качестве трассировок. Сначала установите пакет NuGet `Microsoft.ApplicationInsights.EtwCollector`. Затем измените раздел `TelemetryModules` файла [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

> [!NOTE] 
> События трассировки событий Windows можно собирать только в том случае, если процесс, в котором размещен пакет SDK, выполняется с удостоверением, которое является членом группы "Пользователи журналов производительности" или "Администраторы".

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Для каждого источника можно задать следующие параметры.
 * `ProviderName` указывает имя поставщика трассировки событий Windows для сбора.
 * `ProviderGuid` указывает GUID поставщика трассировки событий Windows для сбора, может использоваться вместо `ProviderName`.
 * `Level` задает уровень ведения журнала для сбора. Возможные значения: `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (необязательный) задает целочисленное значение используемых комбинаций ключевых слов.

## <a name="using-the-trace-api-directly"></a>Непосредственное использование API трассировки
API трассировки в Application Insights можно вызывать напрямую. Адаптеры ведения журналов используют этот API.

Например: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных,  например данных POST.

Кроме того, вы можете настроить для сообщения уровень серьезности. Как и для других данных телеметрии, вы можете добавлять значения свойств, используемые для фильтрации или поиска различных наборов трассировки. Например: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Так вы сможете отфильтровать в колонке [Поиск][diagnostic] все сообщения с нужной степенью серьезности, относящиеся к определенной базе данных.

## <a name="explore-your-logs"></a>Просмотр журналов
Запустите приложение в режиме отладки или разверните его.

В колонке обзора приложения на [портале Application Insights][portal] выберите элемент [Поиск][diagnostic].

Можно, например:

* фильтровать трассировки журнала или элементы с определенными свойствами;
* просматривать подробные сведения конкретного элемента;
* искать другие данные телеметрии, относящиеся к тому же запросу пользователя (то есть с тем же идентификатором операции);
* сохранять конфигурацию этой страницы в списке избранного.

> [!NOTE]
> **Выборка.** Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Дальнейшие действия
[Диагностика ошибок и исключений в ASP.NET][exceptions]

[Дополнительная информация о службе поиска][diagnostic].

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="how-do-i-do-this-for-java"></a>Как это сделать в Java?
Используйте [адаптеры журналов Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>В контекстном меню проекта нет пункта для Application Insights 
* Проверьте, установлены ли на этом компьютере средства Application Insights. В меню "Сервис, расширения и обновления" Visual Studio найдите "Средства Application Insights". Если их нет на вкладке "Установлено", перейдите на вкладку "Интернет" и установите их.
* Возможно, этот тип проекта не поддерживается средствами Application Insights. Используйте [установку вручную](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>В средстве настройки нет параметра для адаптера журналов.
* Сначала необходимо установить платформу ведения журналов.
* Если вы используете System.Diagnostics.Trace, убедитесь, что это средство [настроено в `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Установлена ли у вас последняя версия Application Insights? В меню **Средства** Visual Studio выберите **Расширения и обновления** и откройте вкладку **Обновления**. Если средства Developer Analytics доступны, щелкните, чтобы обновить их.

### <a name="emptykey"></a>Появляется сообщение об ошибке «ключ инструментирования не может быть пустым»
Вероятно, установка пакета Nuget адаптера ведения журнала была выполнена без установки Application Insights.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Я могу видеть трассировки в поиске диагностических данных, но не другие события
Иногда для получения всех событий и запросов через конвейер может потребоваться некоторое время.

### <a name="limits"></a>Какой объем данных сохраняется?
На объем сохраняемых данных влияют несколько факторов. Дополнительные сведения см. на странице метрик событий пользователя в разделе [ограничений](../../azure-monitor/app/api-custom-events-metrics.md#limits). 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Мне не удается найти некоторые записи журнала, которые, как мне кажется, должны там быть
Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Дальнейшие действия
* [Наблюдение за доступностью и скоростью реагирования веб-сайта][availability]
* [Устранение неполадок][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
