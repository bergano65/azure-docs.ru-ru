---
title: Azure Monitor для картирования регионов контейнеров
description: В этой статье описаны отображения регионов, поддерживаемые между Azure Monitor для контейнеров, рабочего пространства для аналитики журналов и пользовательских метрик.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403420"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Картирование регионов, поддерживаемое Azure Monitor для контейнеров

 При включении Azure Monitor для контейнеров поддерживаются только определенные регионы для увязки рабочего пространства журналов Analytics и кластера AKS, а также для сбора пользовательских метрик, представленных в Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Рабочее пространство журнала Analytics поддерживает сяокопию отображений

Ресурсы кластера AKS или рабочее пространство Log Analytics могут проживать в других регионах, а в следующей таблице показаны наши отображения.

|**Кластерный регион АКС** | **Область рабочего пространства аналитики журналов** |
|-----------------------|------------------------------------|
|**Африка** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Австралия** | |
|АвстралияВосток |АвстралияВосток |
|АвстралияЦентральный |АвстралияЦентральный |
|АвстралияЦентральный2 |АвстралияЦентральный |
|АвстралияВосток |АвстралияВосток |
|**Азиатско-Тихоокеанский регион** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Бразилия** | |
|БразилияСаут | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Европа** | |
|ФранцияЦентральный |ФранцияЦентральный |
|FranceSouth |ФранцияЦентральный |
|Североевропа |Североевропа |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Индия** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Япония** | |
|JapanEast |JapanEast |
|ЯпонияЗапад |JapanEast |
|**Корея** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**Нам** | |
|ЦентральныйUS |ЦентральныйUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|Восток США<sup>1</sup>|
|US Gov (Вирджиния) |US Gov (Вирджиния) |

<sup>1</sup> Из-за ограничений в емкости, регион не доступен при создании новых ресурсов. Это включает в себя рабочее пространство log Analytics. Однако существующие в регионе связанные ресурсы должны продолжать работать.

## <a name="custom-metrics-supported-regions"></a>Пользовательские метрики, поддерживаемые регионами

Сбор метрик из узлов и стручков кластеров Azure Kubernetes (AKS) поддерживается для публикации в качестве пользовательских метрик только в [следующих регионах Azure.](../platform/metrics-custom-overview.md#supported-regions)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать мониторинг кластера AKS, [просмотрите, как включить Azure Monitor для контейнеров](container-insights-onboard.md) в соответствии с требованиями и доступными методами для обеспечения мониторинга.  
