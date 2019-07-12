---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Сведения об обновлении кластера Службы Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: dd88b5a044fe495da374178be8774f45bdd30f61
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614063"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить основные компоненты или отдельный пул по умолчанию узел в кластере AKS.

AKS кластеры, использующие несколько пулов узлов или узлов Windows Server (как в настоящее время в предварительной версии в AKS), см. в разделе [обновить пуле узел в AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Перед началом работы

Здесь предполагается, что вы используете Azure CLI версии 2.0.65 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

Чтобы проверить, какие выпуски Kubernetes доступны для кластера, используйте [az aks get обновления][az-aks-get-upgrades] команды. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, обновляет между *1.11.x* -> *1.12.x* или *1.12.x* -> *1.13.x* разрешены, однако *1.11.x* -> *1.13.x* не является.
>
> Чтобы обновить, из *1.11.x* -> *1.13.x*, сначала обновление с *1.11.x* -> *1.12.x*, затем обновления из *1.12.x* -> *1.13.x*.

В следующем примере выходных данных показано, что кластер можно обновить до версии *1.12.7* или *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

Список доступных версий для кластера AKS, использовать [обновление az aks][az-aks-upgrade] command to upgrade. During the upgrade process, AKS adds a new node to the cluster that runs the specified Kubernetes version, then carefully [cordon and drains][kubernetes-drain] один из старого узлов, чтобы свести к минимуму нарушения работы запущенных приложений. Когда новый узел утверждается как запущенные модули приложения, удаляется старый узел. Этот процесс повторяется до обновления всех узлов в кластере.

В следующем примере обновляется кластер до версии *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов.

Чтобы убедиться в успешности обновления, используйте [az aks show][az-aks-show] команды:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластера теперь выполняются *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Следующие шаги

В этой статье было показано, как выполнить обновление существующего кластера AKS. Дополнительные сведения о развертывании AKS и управлении ею см. в следующей статье.

> [!div class="nextstepaction"]
> [Учебники по AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
