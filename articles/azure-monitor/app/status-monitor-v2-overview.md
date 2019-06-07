---
title: Обзор Azure v2 монитор состояния | Документация Майкрософт
description: Обзор монитора состояния v2. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложений ASP.NET, размещенным на предприятиях, на виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734165"
---
# <a name="status-monitor-v2"></a>Монитор состояния версии 2

V2 состояние монитора является опубликован модуль PowerShell [коллекции PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Он заменяет [монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Модуль предоставляет предоставления без помощи кода инструментирование веб-приложений .NET, размещенных в IIS.
Данные телеметрии отправляются на портал Azure, где вы можете [монитор](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) приложения.

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем для рабочих нагрузок. Некоторые функции могут не поддерживаться, а некоторые могут иметь ограниченные возможности.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>Коллекция PowerShell

В коллекции PowerShell находится здесь: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Указания
- См. в разделе [инструкциями по началу работы](status-monitor-v2-get-started.md) работать с образцами краткого кода.
- См. в разделе [подробные инструкции](status-monitor-v2-detailed-instructions.md) подробный обзор о том, как приступить к работе.

## <a name="powershell-api-reference"></a>Справочник по PowerShell API
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Устранение неполадок
- [Устранение неполадок](status-monitor-v2-troubleshoot.md)
- [Известные проблемы](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Часто задаваемые вопросы

- Монитор состояния версии 2 поддерживает установки прокси-сервера?

  *Да*. Существует несколько способов, чтобы скачать монитор состояния v2. Если компьютер имеет доступ к Интернету, вы можете решить эту задачу в коллекции PowerShell с помощью `-Proxy` параметров.
Можно также вручную загрузить модуль и установите его на компьютере или использовать его напрямую.
Каждый из этих параметров описан [подробные инструкции](status-monitor-v2-detailed-instructions.md).
  
- Как проверить, что включение успешно?

   Чтобы убедиться в успешной Включение командлета не существует.
Мы рекомендуем использовать [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) можно быстро определить, если приложение отправляет данные телеметрии.

   Можно также использовать [Log Analytics](../log-query/get-started-portal.md) Чтобы получить список всех ролей облака, в настоящее время отправку данных телеметрии:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Дальнейшие действия

Просмотр телеметрии:

* [Изучение метрик](../../azure-monitor/app/metrics-explorer.md) для контроля производительности и использования.
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
* [Использование аналитики](../../azure-monitor/app/analytics.md) для создания расширенных запросов.
* [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).

Добавление данных телеметрии:

* [Создание веб-тестов](monitor-web-app-availability.md) чтобы убедиться, что ваш сайт продолжает работать.
* [Добавьте телеметрию веб-клиента](../../azure-monitor/app/javascript.md) чтобы просматривать исключения в коде веб-страницы и включить вызовы трассировки.
* [Добавьте пакет SDK Application Insights в код](../../azure-monitor/app/asp-net.md) чтобы можно было вставить трассировки и журналов вызовов.

