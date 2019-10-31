---
title: 'Application Insights: языки, платформы и интеграции | Документация Майкрософт'
description: Языки, платформы и интеграции, доступные для Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 01cebc3a86808d549f1a7dc3adfd2883bc289076
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73061445"
---
# <a name="supported-languages"></a>Поддерживаемые языки

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (Предварительная версия)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Поддерживаемые платформы и среды

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Инструментирование для уже развернутых приложений (без кода, на основе агентов)
* [Виртуальные машины Azure и масштабируемые наборы виртуальных машин Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Служба приложений Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET — для приложений, которые уже доступны в Интернете](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Облачные службы Azure](../../azure-monitor/app/cloudservices.md), в том числе рабочие и веб-роли;
* [Функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Инструментирование с помощью кода (пакеты SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (Предварительная версия)](../../azure-monitor/app/opencensus-python.md)
* [Универсальное приложение для Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Классические приложения, службы и рабочие роли Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Платформы ведения журналов
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog или System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J или Logback](../../azure-monitor/app/java-trace-logs.md)
* [Подключаемый модуль LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Экспорт и анализ данных
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Анализ потока](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Неподдерживаемые пакеты SDK
Мы осведомлены о существовании нескольких других пакетов SDK, поддерживаемых сообществом. Однако Azure Monitor обеспечивает поддержку только при использовании поддерживаемых пакетов SDK, перечисленных на этой странице. Мы постоянно оцениваем возможности, чтобы расширить нашу поддержку для других языков, поэтому на странице [объявления GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) вы получите последние новости пакета SDK. 
