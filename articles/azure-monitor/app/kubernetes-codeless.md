---
title: Мониторинг приложений, выполняемых в службе Azure Kubernetes (AKS), с помощью Application Insights (Azure Monitor) | Документация Майкрософт
description: Azure Monitor без проблем интегрируется с приложением, запущенным в Kubernetes, и позволяет почти мгновенно выявлять проблемы с приложениями.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773759"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Мониторинг приложений, размещенных в Kubernetes, без инструментирования — Application Insights в Azure Monitor

> [!IMPORTANT]
>  Сейчас вы можете включить мониторинг своих приложений Java, работающих в Kubernetes, без инструментирования кода — используйте [автономный агент Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Хотя решение, позволяющее легко включить мониторинг приложений, работает и на других языках, используйте пакеты SDK для мониторинга приложений, выполняемых в AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) и [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Мониторинг приложений без инструментирования кода
В настоящий момент только Java позволяет использовать мониторинг приложений без инструментирования кода. Для мониторинга приложений на других языках используются пакеты SDK. 

## <a name="java"></a>Java
После включения агент Java автоматически соберет множество запросов, зависимостей, журналов и метрик из наиболее популярных библиотек и платформ.

Следуйте [подробным инструкциям](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) для мониторинга приложений Java, выполняемых в приложениях Kubernetes, а также в других средах. 

## <a name="other-languages"></a>Другие языки

Для приложений на других языках сейчас рекомендуется использовать пакеты SDK:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) и [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview).
* Ознакомьтесь с обзором [Распределенной трассировки](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) и узнайте, как [Схемы приложений](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) могут помочь вашему бизнесу.