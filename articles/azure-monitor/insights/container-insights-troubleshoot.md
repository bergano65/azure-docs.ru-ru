---
title: Устранение проблем со службой Azure Monitor для контейнеров (предварительная версия) | Документация Майкрософт
description: В этой статье описывается, как устранить неполадки и проблемы с Azure Monitor для контейнеров.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: magoedte
ms.openlocfilehash: da35f88550bb7bd1a4543f03936dcd52ddd926d9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308365"
---
# <a name="troubleshooting-azure-monitor-for-containers-preview"></a>Устранение проблем со службой Azure Monitor для контейнеров (предварительная версия)

Когда вы настраиваете мониторинг кластера Azure Kubernetes Service (AKS) с помощью Azure Monitor для контейнеров, могут возникнуть проблемы, которые препятствуют сбору данных и отправке отчетов о состоянии. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Служба Azure Monitor для контейнеров включена, но не передает данные
Если служба Azure Monitor для контейнеров включена и настроена, но вы не можете просмотреть сведения о состоянии или результаты не возвращаются в ответ на запрос к журналу Log Analytics, определите проблему, сделав следующее: 

1. Проверьте состояние агента, выполнив эту команду: 

    `kubectl get ds omsagent --namespace=kube-system`

    Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Проверьте состояние развертывания для агента версии *06072018* или более поздней версии с помощью этой команды:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Результат должен выглядеть, как показано в примере ниже, что означает успешное выполнение развертывания:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Проверьте состояние группы pod, чтобы убедиться в ее работоспособности, выполнив эту команду: `kubectl get pods --namespace=kube-system`.

    Выходные данные должны выглядеть, как в примере ниже, и содержать состояние omsagent *Running* (Выполняется).

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Проверьте журналы агента. При развертывании контейнерного агента он выполняет быструю проверку, используя команды OMI, и отображает версию агента и поставщика. 

5. Чтобы проверить, успешно ли подключен агент, выполните эту команду: `kubectl logs omsagent-484hw --namespace=kube-system`.

    Должно отобразиться состояние, как в примере ниже.

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Сообщения об ошибках

В следующей таблице перечислены известные ошибки, которые могут возникнуть при использовании Azure Monitor для контейнеров.

| Сообщения об ошибках  | Действие |  
| ---- | --- |  
| Сообщение об ошибке `No data for selected filters`  | Потребуется некоторое время, чтобы установить мониторинг потока данных для только что созданных кластеров. Подождите примерно 10–15 минут, чтобы данные отобразились для кластера. |   
| Сообщение об ошибке `Error retrieving data` | В то время как кластер службы Azure Kubenetes настраивается для мониторинга работоспособности и производительности, устанавливается соединение между кластером и рабочим пространством Azure Log Analytics. Рабочая область Log Analytics используется для хранения всех данных мониторинга для кластера. Когда рабочая область Log Analytics удалена или утеряна, может возникнуть эта ошибка. Проверьте, доступна ли рабочая область, просмотрев [Сведения о рабочей области](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information). Если рабочая область отсутствует, необходимо повторно установить подключение кластера с помощью Azure Monitor для контейнеров. Чтобы повторно установить подключение, необходимо [отключить](container-insights-optout.md) мониторинг для кластера и [подключить](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) его еще раз для Azure Monitor для контейнеров. |  
| `Error retrieving data` после добавления Azure Monitor для контейнеров с помощью az aks cli | В редких случаях при использовании `az aks cli` Azure Monitor для контейнеров может быть неправильно подключено. Проверьте, подключено ли решение. Чтобы сделать это, перейдите в рабочую область Log Analytics и проверьте, доступно ли решение, выбрав на панели в левой части окна **Решения**. Чтобы устранить эту проблему, необходимо повторно развернуть решение, следуя инструкциям в статье [Подключение Azure Monitor для контейнеров](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json). |  

Чтобы помочь диагностировать данную проблему, [здесь](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script) предоставлен сценарий устранения неполадок.  

## <a name="next-steps"></a>Дополнительная информация
Если включен мониторинг для сбора метрик работоспособности узлов и групп pod кластера AKS, эти метрики будут доступными на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).