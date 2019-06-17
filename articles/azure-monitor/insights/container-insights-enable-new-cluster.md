---
title: Отслеживать кластер Azure Kubernetes Service (AKS) | Документация Майкрософт
description: Узнайте, как включить мониторинг для нового кластера Azure Kubernetes Service (AKS) с помощью Azure Monitor для контейнеров подписки.
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077535"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Включить мониторинг нового кластера Azure Kubernetes Service (AKS)

В этой статье описывается, как настроить Azure Monitor для контейнеров, чтобы отслеживать управляемого кластера Kubernetes, размещенных на [службе Azure Kubernetes](https://docs.microsoft.com/azure/aks/) , которые подготавливаются для развертывания в вашей подписке.

Вы можете включить мониторинг кластера AKS с помощью одного из поддерживаемых методов:

* Инфраструктура CLI Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI

Чтобы включить мониторинг нового кластера AKS, созданного с помощью Azure CLI, следуйте указаниям в разделе [Создание кластера AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.59 или более поздней версии. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Включение с помощью Terraform

Если вы [развертываете новый кластер AKS с помощью Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), нужно указать аргументы, необходимые в профиле [для создания рабочей области Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html), если вы не решили указать уже существующую. 

>[!NOTE]
>Если вы решили использовать Terraform, необходимо запустить Terraform Azure RM Provider версии 1.17.0 или выше.

Чтобы добавить Azure Monitor для контейнеров в рабочую область, просмотрите [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html), заполните профиль, включив [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) и укажите **oms_agent**. 

После включения мониторинга и успешного выполнения всех задач настройки можно отслеживать производительность кластера одним из двух способов:

* Непосредственно в кластере AKS, выбрав **Работоспособность** в левой области.
* Выбрав плитку **Monitor Container insights** (Мониторинг аналитических сведений о контейнере) на странице кластеров AKS для выбранного кластера. В Azure Monitor выберите **Работоспособность** в левой области. 

  ![Параметры для выбора Azure Monitor для контейнеров в AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

После включения мониторинга может пройти около 15 минут, прежде чем вы сможете просмотреть метрики работоспособности кластера. 

## <a name="verify-agent-and-solution-deployment"></a>Проверка развертывания агента и решения
В версии агента *06072018* и выше есть возможность удостовериться, что агент и решение были успешно развернуты. В более ранних версиях агента возможна только проверка развертывания агента.

### <a name="agent-version-06072018-or-later"></a>Версия агента 06072018 или более поздняя
Выполните следующую команду, чтобы проверить успешное развертывание агента. 

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Чтобы проверить развертывание решения, выполните следующую команду:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Агент версии более ранней, чем 06072018

Чтобы проверить, правильно ли развернут агент Log Analytics версии более ранней, чем *06072018*, выполните следующую команду:  

```
kubectl get ds omsagent --namespace=kube-system
```

Результат должен выглядеть приблизительно, как показано ниже, что означает успешное выполнение развертывания:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Просмотр конфигурации с помощью CLI
С помощью команды `aks show` можно получить определенные сведения, например включено ли решение, идентификатор ресурса рабочей области Log Analytics, а также сводные данные о кластере.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Через несколько минут выполнение команды завершается и отображаются сведения о решении в формате JSON.  В результатах выполнения команды должен отобразиться профиль надстройки наблюдения. Вы должны увидеть результат, аналогичный следующему:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Дальнейшие действия

* Если при попытке подключить решение у вас возникли проблемы, ознакомьтесь с [руководством по устранению неполадок](container-insights-troubleshoot.md).

* Если включен мониторинг для сбора метрик работоспособности узлов и контейнеров pod кластера AKS, эти метрики доступны на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).
