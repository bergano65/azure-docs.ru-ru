---
title: Azure Monitor для сопоставлений областей контейнеров
description: В этой статье описываются сопоставления регионов, поддерживаемые между Azure Monitor для контейнеров, Log Analytics рабочей области и настраиваемых метрик.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca77277c8d8d405b8fe81f612a8d7600d3c937ab
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388189"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Сопоставления регионов, поддерживаемые Azure Monitor для контейнеров

 При включении Azure Monitor для контейнеров поддерживаются только определенные регионы для связывания Log Analytics рабочей области и кластера AKS и сбора пользовательских метрик, отправленных на Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Поддерживаемые сопоставления рабочей области Log Analytics

Ресурсы кластера AKS или Рабочая область Log Analytics могут находиться в других регионах, а в следующей таблице приведены сопоставления.

|**Регион кластера AKS** | **Регион Log Analytics рабочей области** |
|-----------------------|------------------------------------|
|**Африка** | |
|саусафриканорс |WestEurope |
|саусафрикавест |WestEurope |
|**Австралия** | |
|AustraliaEast |AustraliaEast |
|аустралиацентрал |аустралиацентрал |
|AustraliaCentral2 |аустралиацентрал |
|AustraliaEast |AustraliaEast |
|**Азиатско-Тихоокеанский регион** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Бразилия** | |
|бразилсаус | SouthCentralUS |
|**Канада** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Европа** | |
|францецентрал |францецентрал |
|францесаус |францецентрал |
|норсеуропе |норсеуропе |
|UKSouth |UKSouth |
|уквест |UKSouth |
|WestEurope |WestEurope |
|**Индия** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Япония** | |
|JapanEast |JapanEast |
|жапанвест |JapanEast |
|**Корея** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**США** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|Штат Виргиния, США |Штат Виргиния, США |

<sup>1</sup> из-за емкости ограничений регион недоступен при создании новых ресурсов. Это относится к рабочей области Log Analytics. Однако уже существующие связанные ресурсы в регионе должны продолжать работать.

## <a name="custom-metrics-supported-regions"></a>Регионы, поддерживаемые пользовательскими метриками

Сбор метрик из узлов кластеров Azure Kubernetes Services (AKS) и модулей Pod поддерживается для публикации в качестве пользовательских метрик только в следующих [регионах Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать мониторинг кластера AKS, ознакомьтесь со сведениями о том, [как включить Azure Monitor для контейнеров](container-insights-onboard.md) , чтобы понять требования и доступные методы для включения мониторинга.  
