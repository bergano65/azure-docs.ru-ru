---
title: Обзор информации об информации о приложениях Azure (ru) Документы Майкрософт
description: Обзор агента по анализу приложений. Мониторинг производительности веб-сайта без передислокации веб-сайта. Работает с ASP.NET веб-приложений, размещенных на базе, в вдовых мыслях или на Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4042fc05f278915fe72bf8fc4e6afd69a5bbb4b9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537429"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Развертывание агента по анализу приложений Azure Monitor для наемных серверов

> [!IMPORTANT]
> Это руководство рекомендуется для развертывания облачных технологий On-Premises и non-Azure в базе агента Application Insights. Вот рекомендуемый подход для [развертывания виртуальных машин Azure и виртуального набора машин.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

Приложение Insights Agent (ранее названный Status Monitor V2) — модуль PowerShell, опубликованный в [галерее PowerShell.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)
Он заменяет [статус монитора](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Телеметрия отправляется на портал Azure, где вы можете [контролировать](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) свое приложение.

> [!NOTE]
> Модуль только в настоящее время поддерживает безкодное оборудование веб-приложений .NET, размещенных с IIS. Используйте SDK для инструментария ASP.NET приложениях Core, Java и Node.js.

## <a name="powershell-gallery"></a>Коллекция PowerShell

Приложение Insights Агент находится https://www.powershellgallery.com/packages/Az.ApplicationMonitorздесь: .

![Коллекция PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Смотрите [инструкции, начинаюе с](status-monitor-v2-get-started.md) начала работы с краткими образцами кода.
- Смотрите [подробные инструкции](status-monitor-v2-detailed-instructions.md) для глубокого погружения о том, как начать работу.

## <a name="powershell-api-reference"></a>Ссылка На API PowerShell
- [Отключить-ApplicationInsightsМониторинг](status-monitor-v2-api-disable-monitoring.md)
- [Отмашина-ИнструментацияEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Включить-ApplicationInsightsМониторинг](status-monitor-v2-api-enable-monitoring.md)
- [Включить-ИнструментированиеEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsМониторинг](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Старт-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Устранение неполадок
- [Устранение неполадок](status-monitor-v2-troubleshoot.md)
- [Известные проблемы](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

- Поддерживает ли агент Application Insights прокси-установки?

  *Да*. Существует несколько способов загрузки агента Application Insights. Если ваш компьютер имеет доступ в Интернет, вы можете `-Proxy` на борту powerShell Галерея с помощью параметров.
Вы также можете вручную загрузить модуль и либо установить его на компьютере или использовать его непосредственно.
Каждый из этих вариантов описан в [подробных инструкциях.](status-monitor-v2-detailed-instructions.md)

- Поддерживает ли Status Monitor v2 ASP.NET основных приложений?

  *No*. Для инструкций, позволяющих осуществлять мониторинг ASP.NET основных приложений, с [ASP.NETм.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) Нет необходимости устанавливать StatusMonitor для приложения ASP.NET Core. Это верно даже в том случае, если ASP.NET приложение Core размещается в IIS.

- Как проверить, что включение удалось?

  - [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet может быть использован для проверки того, что включение удалось.
  - Мы рекомендуем вам использовать [Live Metrics,](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) чтобы быстро определить, отправляет ли ваше приложение телеметрию.

  - Вы также можете использовать [Log Analytics](../log-query/get-started-portal.md) для списка всех ролей облака, отправляющих телеметрию:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Следующие шаги

Просмотр телеметрии:

* [Изучите метрики](../../azure-monitor/platform/metrics-charts.md) для мониторинга производительности и использования.
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
* [Используйте Аналитику](../../azure-monitor/app/analytics.md) для более продвинутых запросов.
* [Создание панелей мониторинга.](../../azure-monitor/app/overview-dashboard.md)

Добавление данных телеметрии:

* [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
* [Добавьте телеметрию веб-клиента,](../../azure-monitor/app/javascript.md) чтобы увидеть исключения из кода веб-страницы и включить отслеживание вызовов.
* [Добавьте SDK Application Insights в свой код,](../../azure-monitor/app/asp-net.md) чтобы вы могли вставлять вызовы трассировки и регистрации.

