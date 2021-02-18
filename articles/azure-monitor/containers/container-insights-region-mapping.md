---
title: Azure Monitor для сопоставлений областей контейнеров
description: Описывает сопоставления регионов, поддерживаемые между Azure Monitor для контейнеров, Log Analytics рабочей области и настраиваемых метрик.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9b77242de3e7845a97874b663266103bf00a8e66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624906"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Сопоставления регионов, поддерживаемые Azure Monitor для контейнеров

 При включении Azure Monitor для контейнеров поддерживаются только определенные регионы для связывания Log Analytics рабочей области и кластера AKS и сбора пользовательских метрик, отправленных на Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Поддерживаемые сопоставления рабочей области Log Analytics

Поддерживаемые регионы AKS перечислены в списке [продукты, доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). Рабочая область Log Analytics должна находиться в том же регионе, за исключением регионов, перечисленных в следующей таблице. Просмотрите [заметки о выпуске AKS](https://github.com/Azure/AKS/releases) для обновлений.


|**Регион кластера AKS** | **Регион Log Analytics рабочей области** |
|-----------------------|------------------------------------|
|**Африка** | |
|саусафриканорс |WestEurope |
|саусафрикавест |WestEurope |
|**Австралия** | |
|AustraliaCentral2 |аустралиацентрал |
|**Бразилия** | |
|бразилсаус | SouthCentralUS |
|**Канада** ||
|CanadaEast |CanadaCentral |
|**Европа** | |
|францесаус |францецентрал |
|уквест |UKSouth |
|**Индия** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Япония** | |
|жапанвест |JapanEast |
|**Корея** | |
|KoreaSouth |KoreaCentral |
|**США** | |
|WestCentralUS<sup>1</sup>|EastUS <sup>1</sup>|


<sup>1</sup> из-за емкости ограничений регион недоступен при создании новых ресурсов. Это относится к рабочей области Log Analytics. Однако уже существующие связанные ресурсы в регионе должны продолжать работать.

## <a name="custom-metrics-supported-regions"></a>Регионы, поддерживаемые пользовательскими метриками

Сбор метрик из узлов кластеров Azure Kubernetes Services (AKS) и модулей Pod поддерживается для публикации в качестве пользовательских метрик только в следующих [регионах Azure](../essentials/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы начать мониторинг кластера AKS, ознакомьтесь со сведениями о том, [как включить Azure Monitor для контейнеров](container-insights-onboard.md) , чтобы понять требования и доступные методы для включения мониторинга.  
