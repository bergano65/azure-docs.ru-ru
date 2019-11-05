---
title: Устранение неполадок с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как устранить неполадки и проблемы с Azure Monitor для контейнеров.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 3d6ed3b13c134d8e9c1df72ae2cb880a477a803a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477043"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Устранение неполадок с Azure Monitor для контейнеров

Когда вы настраиваете мониторинг кластера Azure Kubernetes Service (AKS) с помощью Azure Monitor для контейнеров, могут возникнуть проблемы, которые препятствуют сбору данных и отправке отчетов о состоянии. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Ошибка авторизации во время адаптации или операции обновления
При включении Azure Monitor для контейнеров или обновлении кластера для поддержки сбора метрик может появиться сообщение об ошибке, похожее на следующее: *клиент < удостоверение пользователя > "с идентификатором объекта" < пользователь objectId > "не имеет разрешения на выполнение действия "Microsoft. Authorization/roleAssignments/Write" за пределами области*

В процессе адаптации или обновления предпринимается ошибка назначения роли **издателя метрик мониторинга** для ресурса кластера. Пользователь, инициирующий процесс включения Azure Monitor для контейнеров или обновления для поддержки коллекции метрик, должен иметь доступ к разрешению **Microsoft. Authorization/roleAssignments/Write** в области ресурсов кластера AKS. Доступ к этому разрешению предоставляется только членам встроенных ролей " **владелец** " и " **администратор доступа пользователей** ". Если политикам безопасности требуется назначить разрешения детализированного уровня, рекомендуется просмотреть [пользовательские роли](../../role-based-access-control/custom-roles.md) и назначить их пользователям, которым он необходим. 

Вы также можете вручную предоставить эту роль из портал Azure, выполнив следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com). 
2. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Kubernetes**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Azure Kubernetes**.
3. В списке кластеров Kubernetes выберите один из списка.
2. В меню слева щелкните **Управление доступом (IAM)** .
3. Выберите **+ Добавить** , чтобы добавить назначение роли и выберите роль **издателя метрики мониторинга** и в поле **выбрать** введите **AKS** , чтобы отфильтровать результаты по только тем субъектам службы кластера, которые определены в подписке. Выберите один из списка, относящегося к этому кластеру.
4. Выберите **Сохранить**, чтобы завершить назначение роли. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Служба Azure Monitor для контейнеров включена, но не передает данные
Если Azure Monitor для контейнеров успешно включена и настроена, но вы не можете просматривать сведения о состоянии или не возвращаются результаты из запроса к журналу, вы можете диагностировать проблему, выполнив следующие действия. 

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

5. Чтобы убедиться, что агент успешно развернут, выполните команду: `kubectl logs omsagent-484hw --namespace=kube-system`

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
| Сообщение об ошибке `No data for selected filters`  | Потребуется некоторое время, чтобы установить мониторинг потока данных для только что созданных кластеров. Чтобы данные отображались в кластере, подождите не менее 10 – 15 минут. |   
| Сообщение об ошибке `Error retrieving data` | Хотя кластер службы Kubernetes Azure настраивается для мониторинга работоспособности и производительности, между кластером и рабочей областью Azure Log Analytics устанавливается подключение. Рабочая область Log Analytics используется для хранения всех данных мониторинга для кластера. Эта ошибка может возникать, если Рабочая область Log Analytics удалена. Проверьте, удалена ли Рабочая область и если она была, необходимо снова включить мониторинг кластера с Azure Monitor для контейнеров и указать существующую или создать новую рабочую область. Для повторного включения необходимо [Отключить](container-insights-optout.md) мониторинг кластера и снова [включить](container-insights-enable-new-cluster.md) Azure Monitor для контейнеров. |  
| `Error retrieving data` после добавления Azure Monitor для контейнеров с помощью az aks cli | При включении мониторинга с помощью `az aks cli`Azure Monitor для контейнеров могут быть неправильно развернуты. Проверьте, развернуто ли решение. Чтобы сделать это, перейдите в рабочую область Log Analytics и проверьте, доступно ли решение, выбрав на панели в левой части окна **Решения**. Чтобы устранить эту проблему, необходимо повторно развернуть решение, следуя инструкциям в статье [Подключение Azure Monitor для контейнеров](container-insights-onboard.md). |  

Чтобы помочь диагностировать данную проблему, [здесь](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script) предоставлен сценарий устранения неполадок.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor для контейнеров агентов с контейнерами для реплики не запланированы в кластере Kubernetes, отличной от Azure.

Azure Monitor для модуля "модули реплики агента контейнеров" зависит от следующих селекторов узлов на узлах рабочей роли (или агента) для планирования:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Если к рабочим узлам не подключены Метки узлов, то планирование модулей для реплики агентов не будет запланировано. Инструкции по присоединению метки см. в разделе [Назначение меток Kubernetes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Диаграммы производительности не отображают ЦП или память узлов и контейнеров в кластере, отличном от Azure

Azure Monitor для модулей Pod агента контейнеров используют конечную точку cAdvisor на агенте узла для сбора метрик производительности. Убедитесь, что контейнерный агент на узле настроен на разрешение открытия `cAdvisor port: 10255` на всех узлах в кластере для сбора метрик производительности.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Кластер Kubernetes, не связанный с Azure, не отображается в Azure Monitor для контейнеров

Для просмотра кластера Kubernetes, не относящегося к Azure, в Azure Monitor для контейнеров требуется доступ на чтение в рабочей области Log Analytics, поддерживающей эту аналитику, и в ресурсе Resource Insights **контаинеринсигхтс (*Рабочая область*)** .

## <a name="next-steps"></a>Дальнейшие действия

Если включен мониторинг для сбора метрик работоспособности узлов и контейнеров pod кластера AKS, эти метрики доступны на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).