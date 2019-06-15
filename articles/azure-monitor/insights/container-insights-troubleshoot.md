---
title: Устранение неполадок с Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как устранить неполадки и проблемы с Azure Monitor для контейнеров.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 2e3e39ef24d82393d981c0ce276b3338419e0b2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521773"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Устранение неполадок с Azure Monitor для контейнеров

Когда вы настраиваете мониторинг кластера Azure Kubernetes Service (AKS) с помощью Azure Monitor для контейнеров, могут возникнуть проблемы, которые препятствуют сбору данных и отправке отчетов о состоянии. В этой статье описаны некоторые распространенные проблемы и действия по устранению неполадок.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Ошибка авторизации во время операции подключения или обновления
При включении Azure Monitor для контейнеров или обновление кластера для поддержки сбор метрик, может возникнуть ошибка наподобие следующие значения: *клиента < удостоверение пользователя > "с объектом не имеет идентификатор «< objectId пользователя >» авторизован для выполнения действия «Microsoft.Authorization/roleAssignments/write» с областью*

Во время адаптации или обновления предоставление **мониторинг метрик издателя** попытке назначения роли в ресурс кластера. Пользователь инициирует процесс включает мониторинг Azure для контейнеров или обновление для поддержки сбор показателей должен иметь доступ к **Microsoft.Authorization/roleAssignments/write** разрешение в кластере AKS область ресурсов. Только члены **владельца** и **администратор доступа пользователей** встроенные роли имеют доступ к этому разрешению. Если ваша политика безопасности требует, назначение разрешения на детальном уровне, рекомендуется при просмотре [пользовательские роли](../../role-based-access-control/custom-roles.md) и назначьте его для пользователей, которым он необходим. 

Можно также вручную предоставить этой роли на портале Azure, выполнив следующие действия:

1. Войдите на [портале Azure](https://portal.azure.com). 
2. На портале Azure щелкните **Все службы** в нижнем левом углу. В списке ресурсов введите **Kubernetes**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Azure Kubernetes**.
3. В списке кластеров Kubernetes выберите его из списка.
2. В меню слева щелкните **управление доступом (IAM)** .
3. Выберите **+ добавить** назначение роли и выберите **мониторинг метрик издателя** роли и в разделе **выберите** поле типа **AKS** для Фильтр, результаты в только что кластерах службы субъектов, определенным в подписке. Выберите из списка, относящиеся к этому кластеру.
4. Выберите **Сохранить**, чтобы завершить назначение роли. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Служба Azure Monitor для контейнеров включена, но не передает данные
Если успешно включен и настроен, Azure Monitor для контейнеров, но вы не сможете просматривать сведения о состоянии, результаты не возвращаются из запроса к журналу можно диагностировать проблему, выполнив следующие действия: 

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

5. Чтобы убедиться, что агент успешно развернуто, выполните команду: `kubectl logs omsagent-484hw --namespace=kube-system`

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
| Сообщение об ошибке `No data for selected filters`  | Потребуется некоторое время, чтобы установить мониторинг потока данных для только что созданных кластеров. Разрешить по крайней мере 10 – 15 минут, чтобы данные отобразились для кластера. |   
| Сообщение об ошибке `Error retrieving data` | В то время как кластер службы Azure Kubenetes настраивается для мониторинга работоспособности и производительности, устанавливается соединение между кластером и рабочей областью Azure Log Analytics. Рабочая область Log Analytics используется для хранения всех данных мониторинга для кластера. Когда рабочая область Log Analytics удалена или утеряна, может возникнуть эта ошибка. Проверьте, доступна ли рабочая область, просмотрев [Сведения о рабочей области](../platform/manage-access.md#view-workspace-details). Если рабочая область отсутствует, необходимо будет снова включить мониторинг кластера с помощью Azure Monitor для контейнеров. Чтобы снова включить, необходимо будет [отключить](container-insights-optout.md) мониторинга для кластера и [включить](container-insights-enable-new-cluster.md) Azure Monitor для контейнеров еще раз. |  
| `Error retrieving data` после добавления Azure Monitor для контейнеров с помощью az aks cli | При включении мониторинга с помощью `az aks cli`, Azure Monitor для контейнеров не могут быть развернуты правильно. Проверьте, развернут ли решение. Чтобы сделать это, перейдите в рабочую область Log Analytics и проверьте, доступно ли решение, выбрав на панели в левой части окна **Решения**. Чтобы устранить эту проблему, необходимо повторно развернуть решение, следуя инструкциям в статье [Подключение Azure Monitor для контейнеров](container-insights-onboard.md). |  

Чтобы помочь диагностировать данную проблему, [здесь](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script) предоставлен сценарий устранения неполадок.  

## <a name="next-steps"></a>Дальнейшие действия
Если включен мониторинг для сбора метрик работоспособности узлов и групп pod кластера AKS, эти метрики будут доступными на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).