---
title: Мониторинг локальных приложений Java. Azure Monitor Application Insights
description: Мониторинг производительности локальных приложений Java без инструментирования приложения. Распределенная трассировка и схема приложения.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 04/16/2020
ms.openlocfilehash: aadc9c7bfaa9ad6338015f5dd085b5ab865673f2
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697207"
---
# <a name="java-codeless-application-monitoring-on-premises---azure-monitor-application-insights---public-preview"></a>Локальный мониторинг приложений Java без написания кода. Azure Monitor Application Insights. Общедоступная предварительная версия

Использовать мониторинг приложений Java без написания кода очень просто: не нужно изменять код, а агент Java можно включить, лишь немного изменив конфигурацию.

## <a name="overview"></a>Обзор

После включения агент Java автоматически соберет множество запросов, зависимостей, журналов и метрик из наиболее популярных библиотек и платформ.

Следуйте [подробным инструкциям](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) для всех сред, включая локальную.

 ## <a name="next-steps"></a>Дальнейшие действия

* [Получение инструкций по загрузке агента Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Настройка аргументов виртуальной машины Java](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)
* [Настройка конфигурации](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options)