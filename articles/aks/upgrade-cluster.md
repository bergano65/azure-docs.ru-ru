---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Сведения об обновлении кластера Службы Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66692781"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить основные компоненты или отдельный пул по умолчанию узел в кластере AKS.

AKS кластеры, использующие несколько пулов узлов или узлов Windows Server (как в настоящее время в предварительной версии в AKS), см. в разделе [обновить пуле узел в AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Перед началом работы

Здесь предполагается, что вы используете Azure CLI версии 2.0.65 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

С помощью команды [az aks get-upgrades][az-aks-get-upgrades] проверьте выпуски Kubernetes, доступные для кластера. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

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

С помощью команды [az aks upgrade][az-aks-upgrade] обновите кластер AKS, используя список доступных версий. В процессе обновления AKS добавляет новый узел в кластер под управлением указанной версии Kubernetes, затем внимательно [cordon и продвижения] [ kubernetes-drain] один из старого узлов, чтобы свести к минимуму нарушения работы приложения. Когда новый узел утверждается как запущенные модули приложения, удаляется старый узел. Этот процесс повторяется до обновления всех узлов в кластере.

В следующем примере обновляется кластер до версии *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов.

Чтобы проверить, что обновление выполнено успешно, введите команду [az aks show][az-aks-show].

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластера теперь выполняются *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было показано, как выполнить обновление существующего кластера AKS. Дополнительные сведения о развертывании AKS и управлении ею см. в следующей статье.

> [!div class="nextstepaction"]
> [Руководство. Подготовка приложения для Службы Azure Kubernetes (AKS)][aks-tutorial-prepare-app].

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
