---
title: Автоматический сбор зависимостей в Azure Application Insights | Документация Майкрософт
description: Автоматический сбор и визуализация зависимостей Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109429"
---
# <a name="application-insights-nuget-packages"></a>Пакеты NuGet для Application Insights

Ниже приведен текущий список пакетов NuGet для Application Insights со стабильными выпусками.

## <a name="common-packages-for-aspnet"></a>Общие пакеты для ASP.NET

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Обеспечивает основные функции для передачи всех типов телеметрии Application Insights и является зависимым пакетом для остальных пакетов Application Insights | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Обеспечивает перехват вызовов методов | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Сборщик зависимостей Application Insights для приложений .NET. Это зависимый пакет для пакетов платформы Application Insights, который обеспечивает автоматическую сборку телеметрии зависимости. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Сборщик счетчиков производительности Application Insights позволяет отправлять данные, собранные счетчиками производительности, в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights для веб-приложений .NET | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer; | 2.8.0 | Пакет NuGet Application Insights для Windows Server предоставляет автоматический сбор телеметрии Application Insights для приложений .NET. Этот пакет может использоваться как зависимый для пакетов платформы Application Insights или как изолированный для приложений .NET, которые не охватываются пакетами, соответствующими платформе (например, для рабочих ролей .NET). | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Предоставляет канал телеметрии для пакета SDK Application Insights для Windows Server, который сохранит телеметрию в автономных сценариях. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Общие пакеты для ASP.NET Core

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights для веб-приложений ASP.NET Core. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Этот пакет обеспечивает основные функции для передачи всех типов телеметрии Application Insights и является зависимым пакетом для остальных пакетов Application Insights | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Сборщик зависимостей Application Insights для приложений .NET. Это зависимый пакет для пакетов платформы Application Insights, который обеспечивает автоматическую сборку телеметрии зависимости. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Сборщик счетчиков производительности Application Insights позволяет отправлять данные, собранные счетчиками производительности, в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer; | 2.8.0 | Пакет NuGet Application Insights для Windows Server предоставляет автоматический сбор телеметрии Application Insights для приложений .NET. Этот пакет может использоваться как зависимый для пакетов платформы Application Insights или как изолированный для приложений .NET, которые не охватываются пакетами, соответствующими платформе (например, для рабочих ролей .NET). | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Предоставляет канал телеметрии для пакета SDK Application Insights для Windows Server, который сохранит телеметрию в автономных сценариях. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Прослушиватели, сборщики, аппендеры

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Позволяет пересылать события из DiagnosticSource в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener допускает отправку данных из событий EventSource в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector допускает отправку данных из трассировки событий Windows (ETW) в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Пользовательский TraceListener, который позволяет отправлять сообщения журнала трассировки в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Пользовательский аппендер, который позволяет отправлять сообщения журнала Log4Net в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Пользовательский целевой объект, который позволяет отправлять сообщения журнала NLog в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Отслеживает исключения в приложении и автоматически собирает моментальные снимки для автономного анализа. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Этот пакет предоставляет автоматическое оформление телеметрии с контекстом Service Fabric, в котором запущено приложение. Не используйте этот пакет NuGet для собственных приложений Service Fabric. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Модуль Application Insights для приложений Service Fabric. Используйте этот пакет NuGet только для собственных приложений Service Fabric. Для приложений, выполняющихся в контейнерах, используйте пакет Microsoft.ApplicationInsights.ServiceFabric. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Монитор состояния

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Обеспечивает сбор данных среды выполнения для приложений x64. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Обеспечивает сбор данных среды выполнения для приложений x86. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Эти пакеты являются частью основных функциональных возможностей мониторинга среды выполнения в [мониторе состояния](../../azure-monitor/app/monitor-performance-live-website-now.md). Вам не нужно загружать эти пакеты напрямую, просто используйте установщик монитора состояния. Если вы хотите больше узнать о том, как работают эти пакеты, ознакомьтесь с этой [записью блога](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) от одного из наших разработчиков.

## <a name="additional-packages"></a>Дополнительные пакеты

| Имя пакета | Стабильная версия | ОПИСАНИЕ | Download (Скачать) |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Это расширение обеспечивает мониторинг Application Insights в службе приложений Azure. Пакет SDK версии 2.6.1. Указания: добавьте параметры приложения APPINSIGHTS_INSTRUMENTATIONKEY с ikey и перезапустите веб-приложение, чтобы изменения вступили в силу.| [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Этот пакет содержит файлы, необходимые для внедрения Application Insights без написания кода. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Дополнительная информация

- [Application Insights для ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
- [Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler](../../azure-monitor/app/profiler-aspnetcore-linux.md)
- [Отладочные моментальные снимки для исключений в приложениях .NET](../../azure-monitor/app/snapshot-debugger.md).