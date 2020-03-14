---
title: Как управлять агентом Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается управление наиболее распространенными задачами обслуживания с помощью контейнерного агента Log Analytics, используемого решением "Azure Monitor для контейнеров".
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275325"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Как управлять агентом Azure Monitor для контейнеров

Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. После первоначального развертывания агента есть некоторые рутинные или необязательные задачи, которые нужно будет выполнить в течение его жизненного цикла. В этой статье подробно описывается, как вручную обновить агент и отключить коллекцию переменных среды для определенного контейнера. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Способы обновления агента решения "Azure Monitor для контейнеров"

Решение "Azure Monitor для контейнеров" использует контейнерную версию агента Log Analytics для Linux. При выпуске новой версии агента агент автоматически обновляется на управляемых кластерах Kubernetes, размещенных в службе Kubernetes Azure (AKS) и Azure Red Hat OpenShift. Для [гибридного кластера Kubernetes](container-insights-hybrid-setup.md) агент не управляется, и необходимо вручную обновить агент.

В случае сбоя обновления агента для кластера, размещенного в AKS, в этой статье также описывается процесс обновления агента вручную. Выпущенные версии см. в [объявлениях о выпусках агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Обновление агента в отслеживаемом кластере Kubernetes

Процесс обновления агента в кластерах, кроме Azure Red Hat OpenShift, состоит из двух прямых шагов вперед. Первым шагом является отключение мониторинга с использованием решения "Azure Monitor для контейнеров" с помощью Azure CLI. Выполните действия, описанные в [этой статье](container-insights-optout.md?#azure-cli). С помощью Azure CLI можно удалить агент из узлов в кластере, не влияя на решение и соответствующие данные, хранящиеся в рабочей области. 

>[!NOTE]
>Пока вы выполняете обслуживание, узлы в кластере не переадресовывают собранные данные, а представления производительности не будут отображать данные в период между удалением агента и установкой новой версии. 
>

Чтобы установить новую версию агента, выполните действия, описанные в разделе [Включение мониторинга с помощью Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli), чтобы завершить этот процесс.  

После повторного включения мониторинга может потребоваться около 15 минут, прежде чем можно будет просмотреть обновленные метрики работоспособности для кластера. Чтобы проверить, успешно ли обновлен агент, выполните эту команду: `kubectl logs omsagent-484hw --namespace=kube-system`.

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Обновление агента в гибридном кластере Kubernetes

Процесс обновления агента в кластере Kubernetes, размещенном в локальной среде, AKS Engine в Azure и Azure Stack можно выполнить, выполнив следующую команду:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Если Рабочая область Log Analytics находится в Azure для Китая, выполните следующую команду:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Если Рабочая область Log Analytics находится в Azure для государственных организаций США, выполните следующую команду:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Как отключить коллекцию переменных среды для контейнера

Azure Monitor для контейнеров собирает переменные среды из контейнеров, запущенных в pod, и представляет их на панели свойств выбранного контейнера в представлении **Контейнеры**. Это поведение можно контролировать, отключив коллекцию для определенного контейнера во время развертывания кластера Kubernetes или после установки переменной среды *AZMON_COLLECT_ENV*. Эта возможность доступна в версии агента ciprod11292018 и более поздних версиях.  

Чтобы отключить коллекцию переменных среды для нового или существующего контейнера, задайте переменную **AZMON_COLLECT_ENV** со значением **False** в YAML-файле конфигурации развертывания Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Выполните следующую команду, чтобы применить изменения к кластерам Kubernetes, отличным от Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Чтобы изменить ConfigMap и применить это изменение для кластеров Azure Red Hat OpenShift, выполните следующую команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Откроется текстовый редактор по умолчанию. После задания переменной сохраните файл в редакторе.

Чтобы проверить, действует ли изменение конфигурации, в Azure Monitor для контейнеров выберите контейнер в представлении **Контейнеры**, а затем на панели свойств разверните узел **Переменные среды**.  В разделе должна отображаться только переменная, созданная ранее, — **AZMON_COLLECT_ENV=FALSE**. Для всех других контейнеров в разделе переменных среды должны быть перечислены все обнаруженные переменные среды.

Можно повторно включить обнаружение переменных среды. Для этого примените тот же процесс, что и ранее, и измените значение с **False** на **True**, а затем снова запустите команду `kubectl`, чтобы обновить контейнер.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Дальнейшие действия

Если при попытке обновления агента у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](container-insights-troubleshoot.md).
