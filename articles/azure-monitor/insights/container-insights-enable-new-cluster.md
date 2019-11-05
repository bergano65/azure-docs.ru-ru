---
title: Мониторинг нового кластера Azure Kubernetes Service (AKS) | Документация Майкрософт
description: Узнайте, как включить мониторинг для нового кластера Azure Kubernetes Service (AKS) с Azure Monitor для подписки на контейнеры.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/25/2019
ms.openlocfilehash: 01553eb020c1493488e941705d9df8c8c946340a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478701"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Включение мониторинга нового кластера Azure Kubernetes Service (AKS)

В этой статье описывается, как настроить Azure Monitor для контейнеров, чтобы отслеживать управляемый кластер Kubernetes, размещенный в [службе Kubernetes Azure](https://docs.microsoft.com/azure/aks/) , которая готовится к развертыванию в вашей подписке.

Мониторинг кластера AKS можно включить с помощью одного из поддерживаемых методов:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Включение мониторинга с помощью Azure CLI

Чтобы включить мониторинг нового кластера AKS, созданного с помощью Azure CLI, следуйте указаниям в разделе [Создание кластера AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.74 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Если вы установили расширение CLI AKS-Preview версии 0.4.12 или более поздней, удалите все внесенные изменения, чтобы включить предварительную версию расширения, так как она может переопределить поведение Azure CLI по умолчанию, так как функции предварительной версии AKS недоступны в Azure US министерстве Cloud.

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

* С включенным наблюдением для получения сведений о работоспособности и использовании ресурсов кластером AKS и рабочими нагрузками, которые выполняются на них, Узнайте, [как использовать](container-insights-analyze.md) Azure Monitor для контейнеров.
