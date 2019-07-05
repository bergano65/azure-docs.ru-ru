---
title: Azure Monitor для сопоставления регионов контейнеров
description: В этой статье описываются сопоставления регионов, поддерживаемых между Azure Monitor для контейнеров, рабочей области Log Analytics и пользовательские метрики.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518083"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Сопоставления регионов, поддерживаемых Azure Monitor для контейнеров

 При включении Azure Monitor для контейнеров, только в определенных регионах поддерживаются для связывания с рабочей областью Log Analytics и кластер AKS, и сбор пользовательских метрик отправляется Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Рабочая область log Analytics поддерживается сопоставления

Ресурсы кластера AKS или рабочей области Log Analytics может находиться в других регионах и в следующей таблице показаны наши сопоставления.

|**Регион кластера AKS** | **Область рабочей области Analytics журнала** |
|-----------------------|------------------------------------|
|**Африка** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Австралия** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Азиатско-Тихоокеанский регион** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Бразилия** | |
|BrazilSouth | SouthCentralUS |
|**Канада** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Европа** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Индия** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Япония** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
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

<sup>1</sup> из-за ограничений емкости региона недоступна, при создании новых ресурсов. Это включает рабочую область Log Analytics. Тем не менее созданной ранее связанные ресурсы в регионе должны продолжать работать.

## <a name="custom-metrics-supported-regions"></a>Пользовательские метрики, поддерживаемые регионы

Сбор метрик из служб Azure Kubernetes (AKS) кластеры узлов и модулей POD поддерживаются для публикации как пользовательские метрики только в следующих [регионов Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать мониторинг кластера AKS, просмотрите [Включение монитора Azure для контейнеров](container-insights-onboard.md) сведения о требованиях и доступные методы, чтобы включить мониторинг.  