---
title: Обзор агента Application Insights Azure | Документация Майкрософт
description: Общие сведения об агенте Application Insights. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
ms.openlocfilehash: 294b0d2d91650f33f0b92179a069a8c7cd845525
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389840"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Развертывание агента Application Insights Azure Monitor для локальных серверов

> [!IMPORTANT]
> Это руководство рекомендуется для локальных и не облачных развертываний агента Application Insights. Вот рекомендуемый подход к [развертыванию виртуальных машин Azure и масштабируемых наборов виртуальных машин](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Агент Application Insights (ранее именуемый монитор состояния v2) — это модуль PowerShell, опубликованный в [коллекция PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Он заменяет [Монитор состояния](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Данные телеметрии отправляются в портал Azure, где можно [отслеживать](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) приложение.

> [!NOTE]
> Модуль в настоящее время поддерживает инструментирование без кодирования для веб-приложений .NET, размещенных в IIS. Используйте пакет SDK для инструментирования приложений ASP.NET Core, Java и Node. js.

## <a name="powershell-gallery"></a>Коллекция PowerShell

Application Insights агент расположен здесь: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![Коллекция PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Указания
- Ознакомьтесь с [инструкциями по началу работы](status-monitor-v2-get-started.md) , чтобы получить краткие примеры кода.
- [Подробные инструкции](status-monitor-v2-detailed-instructions.md) по началу работы см. здесь.

## <a name="powershell-api-reference"></a>Справочник по API PowerShell
- [Disable-Аппликатионинсигхтсмониторинг](status-monitor-v2-api-disable-monitoring.md)
- [Disable-Инструментатионенгине](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-Аппликатионинсигхтсмониторинг](status-monitor-v2-api-enable-monitoring.md)
- [Enable-Инструментатионенгине](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-Аппликатионинсигхтсмониторингконфиг](status-monitor-v2-api-get-config.md)
- [Get-Аппликатионинсигхтсмониторингстатус](status-monitor-v2-api-get-status.md)
- [Set-Аппликатионинсигхтсмониторингконфиг](status-monitor-v2-api-set-config.md)
- [Start-Аппликатионинсигхтсмониторингтраце](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Устранение неисправностей
- [Устранение неполадок](status-monitor-v2-troubleshoot.md)
- [Известные проблемы](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Вопросы и ответы

- Поддерживает ли агент Application Insights установку прокси-серверов?

  *Да*. Существует несколько способов загрузки агента Application Insights. Если компьютер имеет доступ к Интернету, можно подключить коллекция PowerShell с помощью параметров `-Proxy`.
Можно также вручную скачать модуль и либо установить его на компьютер, либо использовать его напрямую.
Каждый из этих вариантов описан в [подробных инструкциях](status-monitor-v2-detailed-instructions.md).

- Поддерживает ли монитор состояния v2 ASP.NET Core приложений?

  *Нет*. Инструкции по включению мониторинга ASP.NET Core приложений см. в разделе [Application Insights for ASP.NET Core Applications](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Нет необходимости устанавливать Статусмонитор для приложения ASP.NET Core. Это справедливо, даже если приложение ASP.NET Core размещено в службах IIS.

- Разделы справки убедиться в успешности включения?

  - Командлет [Get-аппликатионинсигхтсмониторингстатус](status-monitor-v2-api-get-status.md) можно использовать для проверки успешности выполнения включения.
  - Мы рекомендуем использовать [динамические метрики](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) , чтобы быстро определить, отправляет ли приложение данные телеметрии.

  - Вы также можете использовать [log Analytics](../log-query/get-started-portal.md) для перечисления всех облачных ролей, отправляющих данные телеметрии:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Дальнейшие действия

Просмотр телеметрии:

* [Изучите метрики](../../azure-monitor/app/metrics-explorer.md) для мониторинга производительности и использования.
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
* [Используйте аналитику](../../azure-monitor/app/analytics.md) для более сложных запросов.
* [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).

Добавление данных телеметрии:

* [Создайте веб-тесты](monitor-web-app-availability.md) , чтобы убедиться, что ваш сайт остается активным.
* [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
* [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставить вызовы трассировки и журнала.

