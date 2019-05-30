---
title: Руководство по Kubernetes в Azure. Обновление кластера
description: В этом руководстве по Службе Azure Kubernetes (AKS) вы узнаете, как обновить имеющийся кластер AKS до последней доступной версии Kubernetes.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 09e3fab67486b4677c5848d81fb39a68e511b6ac
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305207"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Руководство по Обновление Kubernetes в Службе Azure Kubernetes (AKS)

В ходе жизненного цикла приложения и кластера вам может потребоваться выполнить обновления до последней доступной версии Kubernetes и использовать новые функции. Кластер службы Azure Kubernetes (AKS) можно обновить с помощью Azure CLI.

В этом руководстве (часть седьмая из семи) мы обновляем кластер Kubernetes. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера. Этот образ был передан в Реестр контейнеров Azure, и вы создали кластер AKS. Затем приложение было развернуто в кластере AKS. Если вы не выполнили эти действия, вы можете начать с раздела руководства 1 [Создание образов контейнеров][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.53 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Получение доступных версий кластера

Перед обновлением кластера выполните команду [az aks get-upgrades][], чтобы проверить, какие выпуски Kubernetes доступные для этого.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере текущей является версия *1.9.11*, а доступные для обновления представлены в колонке *Обновления*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.9.11           1.9.11             1.10.8, 1.10.9
```

## <a name="upgrade-a-cluster"></a>Обновление кластера

Чтобы свести к минимуму время простоя работающих приложений, узлы AKS тщательно блокируются и останавливаются. В рамках этого процесса выполняются следующие шаги.

1. Планировщик Kubernetes запрещает размещение дополнительных модулей pod на узле, который должен быть обновлен.
1. Для работающих модулей pod на узле планируется выполнение на других узлах в кластере.
1. Создается узел, на котором запускаются новейшие компоненты Kubernetes.
1. Когда новый узел готов и присоединен к кластеру, планировщик Kubernetes начинает запускать модули pod на нем.
1. Старый узел удаляется, а следующий узел в кластере начинает процесс блокировки и остановки.

Для обновления кластера AKS используйте команду [az aks upgrade][]. В следующем примере кластер обновляется до версии Kubernetes *1.10.9*.

> [!NOTE]
> Одновременно можно выполнить только одно обновление дополнительного номера версии. Например, вы можете обновить версию *1.9.11* до *1.10.9*, но невозможно напрямую обновить версию *1.9.6* до *1.11.x*. Чтобы обновить версию *1.9.11* до *1.11.x*, сначала обновите версию *1.9.11* до *1.10.x*, а затем выполните другое обновление с версии *1.10.x* до *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.9
```

Ниже приведен краткий пример выходных данных, где в качестве *kubernetesVersion* показана версия *1.10.9*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.9",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Проверка обновления

Убедитесь, что обновление успешно выполнено, используя команду [az aks show][] следующим образом:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластер AKS выполняется на основе версии *Kubernetes 1.10.9*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.9               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Удаление кластера

Это руководство — последняя часть в этой серии. Вы можете удалить кластер AKS. Поскольку узлы Kubernetes выполняются в виртуальных машинах Azure, за них продолжает начисляться плата, даже если кластер не используется. Используйте команду [az group delete][az-group-delete], чтобы удалить группу ресурсов, службу контейнеров и все связанные с ними ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Когда вы удаляете кластер, субъект-служба Azure Active Directory, используемый в кластере AKS, не удаляется. Инструкции по удалению субъекта-службы см. в разделе [Дополнительные замечания][sp-delete].

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обновили Kubernetes в кластере AKS. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

Перейдите по ссылке для получения дополнительных сведений об AKS.

> [!div class="nextstepaction"]
> [Общие сведения о службе AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
