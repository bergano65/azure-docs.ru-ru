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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184093"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Способы обновления агента решения "Azure Monitor для контейнеров" (предварительная версия)
Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. При выпуске новой версии агент обновляется автоматически в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS).  

В статье описывается, как обновить агент вручную, если произойдет сбой автоматического обновления. Выпущенные версии см. в [объявлениях о выпусках агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Обновление агента на отслеживаемых кластерах Kubernetes
Процесс обновления агента состоит из двух простых шагов. Первым шагом является отключение мониторинга с использованием решения "Azure Monitor для контейнеров" с помощью Azure CLI.  Выполните действия, описанные в [этой статье](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). С помощью Azure CLI можно удалить агент из узлов в кластере, не влияя на решение и соответствующие данные, хранящиеся в рабочей области. 

>[!NOTE]
>Пока вы выполняете обслуживание, узлы в кластере не переадресовывают собранные данные, а представления производительности не будут отображать данные в период между удалением агента и установкой новой версии. 
>

Чтобы установить новую версию агента, выполните действия, описанные в [статье о подключении мониторинга](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) с помощью Azure CLI.  

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть обновленные метрики работоспособности кластера. Чтобы проверить, успешно ли обновлен агент, выполните эту команду: `kubectl logs omsagent-484hw --namespace=kube-system`.

Состояние должно выглядеть примерно так, как в следующем примере, где значения *omi* и *omsagent* должны соответствовать последней версии, указанной в [журнале выпусков агентов](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>Дополнительная информация
Если при попытке обновления агента у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](container-insights-troubleshoot.md).