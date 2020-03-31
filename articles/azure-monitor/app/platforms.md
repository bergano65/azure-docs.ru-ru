---
title: 'Исследования приложений: языки, платформы и интеграции Документы Майкрософт'
description: Языки, платформы и интеграции, доступные для Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136729"
---
# <a name="supported-languages"></a>Поддерживаемые языки

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Javascript](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Поддерживаемые платформы и среды

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Инструменты для уже развернутых приложений (без кода, основанные на агентах)
* [Наборы виртуальных машин Azure VM и Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Служба приложений Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET — для приложений, которые уже доступны в Интернете](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Облачные службы Azure,](../../azure-monitor/app/cloudservices.md)включая роли веб- и рабочего
* [Проверка](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Инструментирование через код (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Универсальное приложение для Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (Центр приложений)
* [Классические приложения, службы и рабочие роли Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Платформы ведения журналов
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog или System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J или Logback](../../azure-monitor/app/java-trace-logs.md)
* [Подключаемый модуль LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Экспорт и анализ данных
* [Мощность BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Неподдерживаемые пакеты SDK
Мы знаем, что существует несколько других SDK, поддерживаемых сообществом. Однако Azure Monitor предоставляет поддержку только при использовании поддерживаемых SDK, перечисленных на этой странице. Мы постоянно оцениваем возможности расширения нашей поддержки других языков, поэтому следите за нашей страницей [объявлений GitHub,](https://github.com/microsoft/ApplicationInsights-Announcements/issues) чтобы получать последние новости SDK. 
