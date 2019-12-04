---
title: включение файла
description: включение файла
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795941"
---
| Ограничение | Описание |
|:---|:---|
| Язык запросов | Azure Monitor использует тот же [язык запросов Kusto](/azure/kusto/query/) , что и обозреватель данных Azure. См. раздел [различия в языке запросов Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) для элементов языка ККЛ, которые не поддерживаются в Azure Monitor. |
| Регионы Azure | Запросы журналов могут испытывать чрезмерную нагрузку, если данные Log Analytics рабочие области в нескольких регионах Azure. Дополнительные сведения см. в разделе [ограничения запросов](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Запросы перекрестных ресурсов | Максимальное число ресурсов Application Insights и Log Analytics рабочих областей в одном запросе ограничено 100.<br>Запрос перекрестных ресурсов не поддерживается в конструкторе представлений.<br>Запрос перекрестных ресурсов в оповещениях журнала поддерживается в новом API Счедуледкуерирулес.<br>Дополнительные сведения см. в разделе [ограничения запросов между ресурсами](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |
| Регулирование запросов | Число запросов для пользователя ограничено 200 запросами на 30 секунд в любом количестве рабочих областей. Это ограничение применяется к программным запросам или к запросам, инициированным такими компонентами визуализации, как панели мониторинга Azure и Сводная страница Log Analytics рабочей области. |
