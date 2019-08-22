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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657853"
---
| Ограничение | Описание |
|:---|:---|
| Язык запросов | Azure Monitor использует тот же [язык запросов Kusto](/azure/kusto/query/) , что и обозреватель данных Azure. См. раздел [различия в языке запросов Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) для элементов языка ККЛ, которые не поддерживаются в Azure Monitor. |
| Регионы Azure | Запросы журналов могут испытывать чрезмерную нагрузку, если данные Log Analytics рабочие области в нескольких регионах Azure. Дополнительные сведения см. в разделе [ограничения запросов](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Запросы перекрестных ресурсов | Максимальное число ресурсов Application Insights и Log Analytics рабочих областей в одном запросе ограничено 100.<br>Запрос перекрестных ресурсов не поддерживается в конструкторе представлений.<br>Запрос перекрестных ресурсов в оповещениях журнала поддерживается в новом API Счедуледкуерирулес.<br>Дополнительные сведения см. в разделе [ограничения запросов между ресурсами](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |