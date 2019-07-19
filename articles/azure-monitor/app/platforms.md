---
title: 'Application Insights: языки, платформы и интеграции | Документация Майкрософт'
description: Языки, платформы и интеграции, доступные для Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/06/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: db79f2b1d3857aac2f71d2e18a3949f068b746eb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990063"
---
# <a name="languages---officially-supported-by-application-insights-team"></a>Языки — официально поддерживается группой Application Insights

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Веб-страницы JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="community-sdks"></a>Пакеты SDK сообщества

Существует ряд пакетов SDK Azure Application Insights для сообщества, многие из которых изначально были созданы корпорацией Майкрософт. Пакеты SDK сообщества официально не обслуживаются корпорацией Майкрософт. Не удалось предоставить поддержку для пакета SDK, который не входит в список официально поддерживаемых. Эти пакеты SDK считаются экспериментальными и не рекомендуются для использования в рабочей среде.

## <a name="platforms-and-frameworks"></a>Платформы и среды
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
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (Центр приложений)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
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
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)
