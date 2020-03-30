---
title: Пакеты Azure Monitor Application Insights NuGet
description: Списки пакетов Azure Monitor Application Insights NuGet для ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670004"
---
# <a name="application-insights-nuget-packages"></a>Пакеты NuGet для Application Insights

Ниже приведен текущий список пакетов NuGet для Application Insights со стабильными выпусками.

## <a name="common-packages-for-aspnet"></a>Общие пакеты для ASP.NET

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Обеспечивает основные функции для передачи всех типов телеметрии Application Insights и является зависимым пакетом для остальных пакетов Application Insights | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Обеспечивает перехват вызовов методов | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Сборщик зависимостей Application Insights для приложений .NET. Это зависимый пакет для пакетов платформы Application Insights, который обеспечивает автоматическую сборку телеметрии зависимости. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Сборщик счетчиков производительности Application Insights позволяет отправлять данные, собранные счетчиками производительности, в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights для веб-приложений .NET | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer; | 2.12.0 | Пакет NuGet Application Insights для Windows Server предоставляет автоматический сбор телеметрии Application Insights для приложений .NET. Этот пакет может использоваться как зависимый для пакетов платформы Application Insights или как изолированный для приложений .NET, которые не охватываются пакетами, соответствующими платформе (например, для рабочих ролей .NET). | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Предоставляет канал телеметрии для пакета SDK Application Insights для Windows Server, который сохранит телеметрию в автономных сценариях. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Общие пакеты для ASP.NET Core

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights для веб-приложений ASP.NET Core. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Этот пакет обеспечивает основные функции для передачи всех типов телеметрии Application Insights и является зависимым пакетом для остальных пакетов Application Insights | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Сборщик зависимостей Application Insights для приложений .NET. Это зависимый пакет для пакетов платформы Application Insights, который обеспечивает автоматическую сборку телеметрии зависимости. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Сборщик счетчиков производительности Application Insights позволяет отправлять данные, собранные счетчиками производительности, в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer; | 2.12.0 | Пакет NuGet Application Insights для Windows Server предоставляет автоматический сбор телеметрии Application Insights для приложений .NET. Этот пакет может использоваться как зависимый для пакетов платформы Application Insights или как изолированный для приложений .NET, которые не охватываются пакетами, соответствующими платформе (например, для рабочих ролей .NET). | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Предоставляет канал телеметрии для пакета SDK Application Insights для Windows Server, который сохранит телеметрию в автономных сценариях. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Общие пакеты для Python с помощью OpenCensus
| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Анализ приложений для python в Azure Monitor через OpenCensus. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [django.](https://pypi.org/project/django/) | [Загрузить пакет](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-фляг | 0.7.3 | Этот пакет обеспечивает интеграцию с библиотекой [флягов](https://pypi.org/project/flask/) Python. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [http.client](https://docs.python.org/3/library/http.client.html) для Python3 и [httplib](https://docs.python.org/2/library/httplib.html) для Python2. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | Этот пакет обогащает записи журналов данными о следах. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [mysql-connector.](https://pypi.org/project/mysql-connector/) | [Загрузить пакет](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [psycopg2.](https://pypi.org/project/psycopg2/) | [Загрузить пакет](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Этот пакет обеспечивает интеграцию с библиотекой [pymongo](https://pypi.org/project/pymongo/) Python. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [PyMyS'L.](https://pypi.org/project/PyMySQL/) | [Загрузить пакет](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-пирамида | 0.7.1 | Этот пакет обеспечивает интеграцию с библиотекой [пирамид](https://pypi.org/project/pyramid/) Python. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-requests | 0.7.2 | Этот пакет обеспечивает интеграцию с библиотекой [запросов](https://pypi.org/project/requests/) Python. | [Загрузить пакет](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Этот пакет обеспечивает интеграцию с библиотекой Python [S'LAlchemy.](https://pypi.org/project/SQLAlchemy/) | [Загрузить пакет](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Прослушиватели, сборщики, аппендеры

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Позволяет пересылать события из DiagnosticSource в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener допускает отправку данных из событий EventSource в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector допускает отправку данных из трассировки событий Windows (ETW) в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Пользовательский TraceListener, который позволяет отправлять сообщения журнала трассировки в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Пользовательский аппендер, который позволяет отправлять сообщения журнала Log4Net в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Пользовательский целевой объект, который позволяет отправлять сообщения журнала NLog в Application Insights. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Отслеживает исключения в приложении и автоматически собирает моментальные снимки для автономного анализа. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Этот пакет предоставляет автоматическое оформление телеметрии с контекстом Service Fabric, в котором запущено приложение. Не используйте этот пакет NuGet для собственных приложений Service Fabric. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Модуль Application Insights для приложений Service Fabric. Используйте этот пакет NuGet только для собственных приложений Service Fabric. Для приложений, выполняющихся в контейнерах, используйте пакет Microsoft.ApplicationInsights.ServiceFabric. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Монитор состояния

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Обеспечивает сбор данных среды выполнения для приложений x64. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Обеспечивает сбор данных среды выполнения для приложений x86. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Эти пакеты являются частью основных функциональных возможностей мониторинга среды выполнения в [мониторе состояния](../../azure-monitor/app/monitor-performance-live-website-now.md). Вам не нужно загружать эти пакеты напрямую, просто используйте установщик монитора состояния. Если вы хотите больше узнать о том, как работают эти пакеты, ознакомьтесь с этой [записью блога](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) от одного из наших разработчиков.

## <a name="additional-packages"></a>Дополнительные пакеты

| Имя пакета | Стабильная версия | Описание | Скачивание |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Это расширение обеспечивает мониторинг Application Insights в службе приложений Azure. Пакет SDK версии 2.6.1. Указания: добавьте параметры приложения "APPINSIGHTS_INSTRUMENTATIONKEY" с ikey и перезапустите веб-приложение, чтобы изменения вступили в силу.| [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Этот пакет содержит файлы, необходимые для внедрения Application Insights без написания кода. | [Загрузить пакет](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Дальнейшие действия

- [Application Insights для ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
- [Профилирование веб-приложений ASP.NET Core в Azure для Linux с помощью Application Insights Profiler](../../azure-monitor/app/profiler-aspnetcore-linux.md)
- [Отладочные моментальные снимки для исключений в приложениях .NET](../../azure-monitor/app/snapshot-debugger.md).
