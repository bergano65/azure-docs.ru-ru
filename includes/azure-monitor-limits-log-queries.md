---
title: включить файл
description: включить файл
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795941"
---
| Ограничение | Описание |
|:---|:---|
| Язык запросов | Azure Monitor использует тот же [язык запросов Kusto,](/azure/kusto/query/) что и Azure Data Explorer. См. [Различия в языке запросов запросов Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) для элементов языка КЗЛ, не поддерживаемых в Azure Monitor. |
| Регионы Azure | Запросы журнала могут испытывать чрезмерные накладные расходы, когда данные охватывают рабочие области Log Analytics в нескольких регионах Azure. Подробнее о [границах запросов.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Перекрестные запросы ресурсов | Максимальное количество ресурсов Application Insights и рабочих областей анализа журналов в одном запросе ограничено 100.<br>Кросс-ресурсный запрос не поддерживается в «Квью-конструктор».<br>Запрос между разными ресурсами в оповещениях журнала поддерживается в новом API правил запросов по расписанию.<br>См [с информации о границах запросов на перекрестные ресурсы.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |
| Регулирование запросов | Пользователь ограничен 200 запросами в течение 30 секунд на любом количестве рабочих областей. Это ограничение распространяется на программные запросы или на запросы, инициированные такими составляющими визуализации, как панели мониторинга Azure и страница сводки рабочего пространства анализа log Analytics. |
