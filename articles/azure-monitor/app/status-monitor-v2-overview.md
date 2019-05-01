---
title: Обзор Azure v2 монитор состояния | Документация Майкрософт
description: Обзор монитора состояния v2. Мониторинг производительности веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, на виртуальных машинах или в Azure.
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
ms.openlocfilehash: fac14281365ccf3c191684af8cfdebda69e734e0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870452"
---
# <a name="status-monitor-v2"></a>Состояние монитора v2

Состояние монитора v2 представляет собой модуль PowerShell, опубликованные в [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) и является заменой для [монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Этот модуль предоставляет инструментарий без использования кода .NET веб-приложений, размещенных в IIS.
Данные телеметрии отправляются на портал Azure здесь можно [монитор](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) приложения.

> [!IMPORTANT]
> Состояние монитора v2 в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в разделе [дополнительные условия использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>Коллекция PowerShell

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Указания
- Просмотрите наши [инструкциями по началу работы](status-monitor-v2-get-started.md) быстро начать работу с образцами краткого кода.
- Просмотрите наши [подробные инструкции](status-monitor-v2-detailed-instructions.md) подробный обзор о том, как приступить к работе.

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

  **Да**. Существует несколько способов, чтобы скачать монитор состояния v2. Если компьютер имеет доступ к Интернету, вы сможете подключать к коллекции PowerShell с помощью `-Proxy` параметров. Можно также вручную загрузить этот модуль и установите его на компьютере или с помощью модуля напрямую. Каждый из этих параметров описан в наших [подробные инструкции](status-monitor-v2-detailed-instructions.md).
  
- Проверка подключения выполнена успешно?

   Нам не нужно проверить успешность этого Включение командлета. Мы рекомендуем использовать [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) Чтобы быстро просматривать, если приложение может отправлять нам данные телеметрии.
