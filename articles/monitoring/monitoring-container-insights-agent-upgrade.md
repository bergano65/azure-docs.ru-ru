---
title: Способы обновления агента решения "Azure Monitor для контейнеров" (предварительная версия) | Документация Майкрософт
description: В этой статье описывается, как обновить агент Log Analytics, используемый решением "Azure Monitor для контейнеров".
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628752"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Способы обновления агента решения "Azure Monitor для контейнеров" (предварительная версия)
Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. При выпуске новой версии агент не обновляется автоматически в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes.

В этой статье описывается процесс обновления агента.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Обновление агента на отслеживаемых кластерах Kubernetes
Процесс обновления агента состоит из двух простых шагов. Первым шагом является отключение мониторинга с использованием решения "Azure Monitor для контейнеров" с помощью Azure CLI.  Выполните действия, описанные в [этой статье](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). С помощью Azure CLI можно удалить агент из узлов в кластере, не влияя на решение и соответствующие данные, хранящиеся в рабочей области. 

>[!NOTE]
>Пока вы выполняете обслуживание, узлы в кластере не переадресовывают собранные данные, а представления производительности не будут отображать данные в период между удалением агента и установкой новой версии. 
>

Чтобы установить новую версию агента, выполните действия, описанные в [статье о подключении мониторинга](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) с помощью Azure CLI.  

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть обновленные метрики работоспособности кластера. 

## <a name="next-steps"></a>Дополнительная информация
Если при попытке обновления агента у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](monitoring-container-insights-troubleshoot.md).