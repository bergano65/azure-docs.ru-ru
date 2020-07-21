---
title: Обзор агента Application Insights Azure | Документация Майкрософт
description: Общие сведения об агенте Application Insights. Отслеживайте производительность веб-сайта без повторного развертывания веб-сайта. Работает с веб-приложениями ASP.NET, размещенными локально, в виртуальных машинах или в Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 6a51da4f4685d7d7b1c597d8a9b9dd78270f29b1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499279"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Развертывание агента Application Insights Azure Monitor для локальных серверов

> [!IMPORTANT]
> Это руководство рекомендуется для локальных и не облачных развертываний агента Application Insights. Вот рекомендуемый подход к [развертыванию виртуальных машин Azure и масштабируемых наборов виртуальных машин](./azure-vm-vmss-apps.md).

Агент Application Insights (ранее именуемый монитор состояния v2) — это модуль PowerShell, опубликованный в [коллекция PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Он заменяет [Монитор состояния](./monitor-performance-live-website-now.md).
Данные телеметрии отправляются в портал Azure, где можно [отслеживать](./app-insights-overview.md) приложение.

> [!NOTE]
> Модуль в настоящее время поддерживает инструментирование без кодирования для веб-приложений .NET, размещенных в IIS. Используйте пакет SDK для инструментирования приложений ASP.NET Core, Java и Node.js.

## <a name="powershell-gallery"></a>Коллекция PowerShell

Application Insights агент расположен здесь: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Коллекция PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Ознакомьтесь с [инструкциями по началу работы](status-monitor-v2-get-started.md) , чтобы получить краткие примеры кода.
- [Подробные инструкции](status-monitor-v2-detailed-instructions.md) по началу работы см. здесь.

## <a name="powershell-api-reference"></a>Справочник по API PowerShell
- [Disable-Аппликатионинсигхтсмониторинг](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-Инструментатионенгине](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-Аппликатионинсигхтсмониторинг](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-Инструментатионенгине](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-Аппликатионинсигхтсмониторингконфиг](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-Аппликатионинсигхтсмониторингстатус](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-Аппликатионинсигхтсмониторингконфиг](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-Аппликатионинсигхтсмониторингтраце](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Устранение неполадок
- [Устранение неполадок](status-monitor-v2-troubleshoot.md)
- [Известные проблемы](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>часто задаваемые вопросы

- Поддерживает ли агент Application Insights установку прокси-серверов?

  *Да*. Существует несколько способов загрузки агента Application Insights. Если компьютер имеет доступ к Интернету, можно подключить коллекция PowerShell с помощью `-Proxy` параметров.
Можно также вручную скачать модуль и либо установить его на компьютер, либо использовать его напрямую.
Каждый из этих вариантов описан в [подробных инструкциях](status-monitor-v2-detailed-instructions.md).

- Поддерживает ли монитор состояния v2 ASP.NET Core приложений?

  *Нет*. Инструкции по включению мониторинга ASP.NET Core приложений см. в разделе [Application Insights for ASP.NET Core Applications](./asp-net-core.md). Нет необходимости устанавливать Статусмонитор для приложения ASP.NET Core. Это справедливо, даже если приложение ASP.NET Core размещено в службах IIS.

- Разделы справки убедиться в успешности включения?

  - Командлет [Get-аппликатионинсигхтсмониторингстатус](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) можно использовать для проверки успешности выполнения включения.
  - Мы рекомендуем использовать [динамические метрики](./live-stream.md) , чтобы быстро определить, отправляет ли приложение данные телеметрии.

  - Вы также можете использовать [log Analytics](../log-query/get-started-portal.md) для перечисления всех облачных ролей, отправляющих данные телеметрии:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Дальнейшие действия

Просмотр телеметрии:

* [Изучите метрики](../../azure-monitor/platform/metrics-charts.md) для мониторинга производительности и использования.
* [Поиск событий и журналов](../../azure-monitor/app/diagnostic-search.md) для диагностики проблем.
* [Используйте аналитику](../log-query/log-query-overview.md) для более сложных запросов.
* [Создание панелей мониторинга](../../azure-monitor/app/overview-dashboard.md).

Добавление данных телеметрии:

* [Создайте веб-тесты](monitor-web-app-availability.md), чтобы убедиться, что ваш сайт продолжает работать.
* [Добавьте данные телеметрии веб-клиента](../../azure-monitor/app/javascript.md) , чтобы просмотреть исключения из кода веб-страницы и включить вызовы трассировки.
* [Добавьте в код пакет SDK для Application Insights](../../azure-monitor/app/asp-net.md) , чтобы можно было вставить вызовы трассировки и журнала.
