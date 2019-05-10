---
title: Максимальная польза от Azure Application Insights | Документация Майкрософт
description: Начав работу с Application Insights, ознакомьтесь с этим списком доступных функций.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7e62efb0b64b0b882b36126af4a1f9d520d92475
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230635"
---
# <a name="more-telemetry-from-application-insights"></a>Дополнительные данные телеметрии из Application Insights
После [добавления Application Insights в код ASP.NET](../../azure-monitor/app/asp-net.md)можно сделать еще кое-что, чтобы получать дополнительные данные телеметрии. 

| Действие | Что вы получаете|
|---|---|
|(Серверы IIS) [Установите монитор состояния](https://go.microsoft.com/fwlink/?LinkId=506648) на каждом компьютере-сервере.<br/>(Веб-приложения Azure) На панели управления Azure веб-приложения откройте колонку Application Insights.| [**Счетчики производительности**](../../azure-monitor/app/performance-counters.md).<br/>[**Исключения**](asp-net-exceptions.md) — подробные трассировки стека.<br/>[**Зависимости**](../../azure-monitor/app/asp-net-dependencies.md).|
|[Добавьте фрагмент JavaScript в свои веб-страницы](../../azure-monitor/app/javascript.md)|[Производительность страниц](../../azure-monitor/app/usage-overview.md), исключения браузера, производительность вызовов AJAX. Пользовательская телеметрия на стороне клиента.|
|[Создайте веб-тесты на доступность](../../azure-monitor/app/monitor-web-app-availability.md)|Получение оповещений, когда сайт становится недоступным|
|Убедитесь, что MSBuild создает [файл BuildInfo.config](https://msdn.microsoft.com/library/dn449058.aspx)|[Создание заметок к диаграммам метрик](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Напишите собственные события и систему телеметрии](../../azure-monitor/app/api-custom-events-metrics.md)|Учет бизнес-событий и метрик, отслеживание подробных сведений об использовании и многое другое|
|[Выполните профилирование активного сайта](https://aka.ms/AIProfilerPreview)|Подробные временные показатели функций из активного веб-приложения|






