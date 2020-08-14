---
title: 'Application Insights: языки, платформы и интеграции | Документация Майкрософт'
description: Языки, платформы и интеграции, доступные для Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d3bb1473d94db04d1dedf2b09e19b83197cc6e0e
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225110"
---
# <a name="supported-languages"></a>Поддерживаемые языки

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Поддерживаемые платформы и среды

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Инструментирование для уже развернутых приложений (без кода, на основе агентов)
* [Виртуальные машины Azure и масштабируемые наборы виртуальных машин Azure](./azure-vm-vmss-apps.md)
* [Служба приложений Azure](./azure-web-apps.md)
* [ASP.NET — для приложений, которые уже доступны в Интернете](./monitor-performance-live-website-now.md)
* [Облачные службы Azure](./cloudservices.md), в том числе рабочие и веб-роли;
* [Функции Azure](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Инструментирование с помощью кода (пакеты SDK)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../learn/mobile-center-quickstart.md) (Центр приложений)
* [iOS](../learn/mobile-center-quickstart.md) (Центр приложений)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Универсальное приложение для Windows](../learn/mobile-center-quickstart.md) (Центр приложений)
* [Классические приложения, службы и рабочие роли Windows](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Платформы ведения журналов
* [ILogger](./ilogger.md)
* [Log4Net, NLog или System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J или Logback](./java-trace-logs.md)
* [Подключаемый модуль LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Экспорт и анализ данных
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Неподдерживаемые пакеты SDK
Мы осведомлены о существовании нескольких других пакетов SDK, поддерживаемых сообществом. Однако Azure Monitor обеспечивает поддержку только при использовании поддерживаемых пакетов SDK, перечисленных на этой странице. Мы постоянно оцениваем возможности, чтобы расширить нашу поддержку для других языков, поэтому на странице [объявления GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) вы получите последние новости пакета SDK. 

