---
title: Как управлять агентом Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается управление наиболее распространенными задачами обслуживания с помощью контейнерного агента Log Analytics, используемого решением "Azure Monitor для контейнеров".
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 566ab8d14ebce04a2cba208dd72efc3782d5ad41
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256304"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Как управлять агентом Azure Monitor для контейнеров
Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. После первоначального развертывания агента есть некоторые рутинные или необязательные задачи, которые нужно будет выполнить в течение его жизненного цикла. В этой статье подробно описывается, как вручную обновить агент и отключить коллекцию переменных среды для определенного контейнера. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Способы обновления агента решения "Azure Monitor для контейнеров"
Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. При выпуске новой версии агент обновляется автоматически в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS).  

В статье описывается, как обновить агент вручную если произойдет сбой автоматического обновления. Выпущенные версии см. в [объявлениях о выпусках агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Обновление агента на отслеживаемых кластерах Kubernetes
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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Как отключить коллекцию переменных среды для контейнера
Azure Monitor для контейнеров собирает переменные среды из контейнеров, запущенных в pod, и представляет их на панели свойств выбранного контейнера в представлении **Контейнеры**. Этим поведением можно управлять путем отключения коллекции для определенного контейнера либо во время развертывания кластера AKS, либо после, задав переменную среды *AZMON_COLLECT_ENV*. Эта возможность доступна в версии агента ciprod11292018 и более поздних версиях.  

Чтобы отключить коллекцию переменных среды для нового или существующего контейнера, задайте переменную **AZMON_COLLECT_ENV** со значением **False** в YAML-файле конфигурации развертывания Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Выполните следующую команду, чтобы применить изменение к контейнеру AKS: `kubectl apply -f  <path to yaml file>`.

Чтобы проверить, действует ли изменение конфигурации, в Azure Monitor для контейнеров выберите контейнер в представлении **Контейнеры**, а затем на панели свойств разверните узел **Переменные среды**.  В разделе должна отображаться только переменная, созданная ранее, — **AZMON_COLLECT_ENV=FALSE**. Для всех других контейнеров в разделе переменных среды должны быть перечислены все обнаруженные переменные среды.   

Можно повторно включить обнаружение переменных среды. Для этого примените тот же процесс, что и ранее, и измените значение с **False** на **True**, а затем снова запустите команду `kubectl`, чтобы обновить контейнер.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Дополнительная информация
Если при попытке обновления агента у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](container-insights-troubleshoot.md).