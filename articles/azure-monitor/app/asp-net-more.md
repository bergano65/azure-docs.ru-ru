---
title: Максимальная польза от Azure Application Insights | Документация Майкрософт
description: Начав работу с Application Insights, ознакомьтесь с этим списком доступных функций.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321349"
---
# <a name="more-telemetry-from-application-insights"></a>Дополнительные данные телеметрии из Application Insights
После [добавления Application Insights в код ASP.NET](./asp-net.md)можно сделать еще кое-что, чтобы получать дополнительные данные телеметрии. 

| Действие | Что вы получаете|
|---|---|
|(Серверы IIS) [Установите монитор состояния](https://go.microsoft.com/fwlink/?LinkId=506648) на каждом компьютере-сервере.<br/>(Веб-приложения Azure) На панели управления Azure веб-приложения откройте колонку Application Insights.| [**Счетчики производительности**](./performance-counters.md)<br/>[**Исключения**](asp-net-exceptions.md) — подробные трассировки стека.<br/>[**Зависимости**](./asp-net-dependencies.md)|
|[Добавьте фрагмент JavaScript в свои веб-страницы](./javascript.md)|[Производительность страниц](./usage-overview.md), исключения браузера, производительность вызовов AJAX. Пользовательская телеметрия на стороне клиента.|
|[Создайте веб-тесты на доступность](./monitor-web-app-availability.md)|Получение оповещений, когда сайт становится недоступным|
|Убедитесь, что MSBuild создает [файл BuildInfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015)|[Создание заметок к диаграммам метрик](./annotations.md)
|[Напишите собственные события и систему телеметрии](./api-custom-events-metrics.md)|Учет бизнес-событий и метрик, отслеживание подробных сведений об использовании и многое другое|
|[Выполните профилирование активного сайта](https://aka.ms/AIProfilerPreview)|Подробные временные показатели функций из активного веб-приложения|

