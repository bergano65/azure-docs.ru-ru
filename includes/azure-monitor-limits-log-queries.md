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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405666"
---
| Ограничение | Описание |
|:---|:---|
| Язык запросов | Azure Monitor использует тот же [язык запросов Kusto](/azure/kusto/query/) , что и обозреватель данных Azure. См. раздел [различия в языке запросов Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) для элементов языка ККЛ, которые не поддерживаются в Azure Monitor. |
| Регионы Azure | Запросы журналов могут испытывать чрезмерную нагрузку, если данные Log Analytics рабочие области в нескольких регионах Azure. Дополнительные сведения см. в разделе [ограничения запросов](../articles/azure-monitor/log-query/scope.md#query-limits) . |
