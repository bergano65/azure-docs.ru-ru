---
title: Устранение неполадок с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как устранить неполадки и проблемы с Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403382"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Устранение неполадок с Azure Monitor для контейнеров

Когда вы настраиваете мониторинг кластера Azure Kubernetes Service (AKS) с помощью Azure Monitor для контейнеров, могут возникнуть проблемы, которые препятствуют сбору данных и отправке отчетов о состоянии. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Ошибка авторизации во время операции на борту или обновления
В ключине Azure Monitor для контейнеров или обновление кластера для поддержки метрик сбора, вы можете получить ошибку, напоминающую следующее - *Клиент <> идентификации пользователя» с идентификатором объекта "<пользовательobjectId>" не имеет разрешения на выполнение действий 'Microsoft.Authorization/roleAssignments/write' над областью*

В процессе работы или обновления на ресурсе кластера выполняется попытка предоставления назначения роли **издателя мониторинговых** метрик. Пользователь, инацизущий процесс включения Azure Monitor для контейнеров или обновления для поддержки сбора метрик, должен иметь доступ к **Microsoft.Authorization/roleAssignments/write** permission на области кластерных ресурсов кластера AKS. Только членам встроенных ролей **Владельца** и **Администратора пользовательского доступа** предоставляется доступ к этому разрешению. Если политикам безопасности требуется назначение разрешений на гранулированный уровень, мы рекомендуем просмотреть [пользовательские роли](../../role-based-access-control/custom-roles.md) и назначить их пользователям, которые в них нуждаются. 

Вы также можете вручную предоставить эту роль с портала Azure, выполнив следующие действия:

1. Войдите на [портал Azure](https://portal.azure.com). 
2. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов, введите **Kubernetes**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Azure Kubernetes**.
3. В списке кластеров Kubernetes выберите один из списка.
2. Из меню слева щелкните **элемент управления доступом (IAM).**
3. Выберите **: Добавьте,** чтобы добавить назначение ролей и выберите роль **издателя метикмониторинга мониторинга** и под типом **окна Select** **Box AKS** для фильтрации результатов только на принципах службкластеров, определенных в подписке. Выберите список из списка, который специфичен для этого кластера.
4. Выберите **Сохранить**, чтобы завершить назначение роли. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Служба Azure Monitor для контейнеров включена, но не передает данные
Если Azure Monitor для контейнеров успешно включен и настроен, но вы не можете просматривать информацию о статусе или результаты не возвращаются из запроса журнала, вы диагностировать проблему, выполнив следующие следующие действия: 

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

5. Чтобы убедиться, что агент был успешно развернут, запустите команду:`kubectl logs omsagent-484hw --namespace=kube-system`

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
| Сообщение об ошибке `No data for selected filters`  | Потребуется некоторое время, чтобы установить мониторинг потока данных для только что созданных кластеров. Разрешить по крайней мере от 10 до 15 минут для данных, чтобы появиться для кластера. |   
| Сообщение об ошибке `Error retrieving data` | В то время как кластер службы Azure Kubernetes создается для мониторинга работоспособности и производительности, устанавливается соединение между кластером и рабочим пространством Analytics журналов Azure. Рабочая область Log Analytics используется для хранения всех данных мониторинга для кластера. Эта ошибка может возникнуть при удалении рабочего пространства Log Analytics. Проверьте, было ли рабочее пространство удалено и было ли оно, необходимо повторно включить мониторинг кластера с помощью Azure Monitor для контейнеров и указать существующее или создать новое рабочее пространство. Для повторного включения необходимо [отключить](container-insights-optout.md) мониторинг кластера и снова [включить](container-insights-enable-new-cluster.md) Azure Monitor для контейнеров. |  
| `Error retrieving data` после добавления Azure Monitor для контейнеров с помощью az aks cli | При использовании `az aks cli`мониторинга Azure Monitor для контейнеров может быть неправильно развернут. Проверьте, развернуто ли решение. Чтобы сделать это, перейдите в рабочую область Log Analytics и проверьте, доступно ли решение, выбрав на панели в левой части окна **Решения**. Чтобы устранить эту проблему, необходимо повторно развернуть решение, следуя инструкциям в статье [Подключение Azure Monitor для контейнеров](container-insights-onboard.md). |  

Чтобы помочь диагностировать данную проблему, [здесь](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script) предоставлен сценарий устранения неполадок.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor для контейнеров агента ReplicaSet Pods не планируется в кластере non-Azure Kubernetes

Azure Monitor для контейнеров агента ReplicaSet Pods имеет зависимость от следующих селекторов узлов узлов для рабочего (или агента) для планирования:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Если у узлы рабочих не имеют прикрепленных меток узлов, то стручки агента ReplicaSet не будут запланированы. Обратитесь к [Kubernetes назначить селекторы метки](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) для инструкций о том, как прикрепить этикетку.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Диаграммы производительности не отображения процессора или памяти узлов и контейнеров в кластере, не относящемся к Azure

Azure Monitor для контейнеров агент Pods использует конечную точку cAdvisor на агенте узла для сбора метрик производительности. Проверка контейнерного агента на узлах настроена таким образом, чтобы можно было `cAdvisor port: 10255` открыть все узлы кластера для сбора метрик производительности.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Кластер Non-Azure Kubernetes не отображается в Azure Monitor для контейнеров

Для просмотра кластера неAzure Kubernetes в Azure Monitor для контейнеров требуется доступ Кread access в рабочем пространстве Log Analytics, поддерживающем эту информацию Insight, и в ресурсе контейнерных insights **ContainerInsights *(рабочее пространство).***

## <a name="next-steps"></a>Дальнейшие действия

Если включен мониторинг для сбора метрик работоспособности узлов и групп pod кластера AKS, эти метрики будут доступными на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).
