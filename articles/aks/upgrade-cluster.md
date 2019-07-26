---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Сведения об обновлении кластера Службы Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2ed58846b9e7816092f0fc0787204921071d75e9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498556"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить главные компоненты или один пул узлов по умолчанию в кластере AKS.

Для кластеров AKS, использующих несколько пулов узлов или узлов Windows Server (в настоящее время в предварительной версии в AKS), см. статью [Обновление пула узлов в AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей требуется Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

> [!WARNING]
> Обновление кластера AKS активирует Cordon и сток узлов. При наличии свободной квоты вычислений обновление может завершиться ошибкой.  Дополнительные сведения см. в разделе [увеличение квот](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

Чтобы проверить, какие выпуски Kubernetes доступны для кластера, используйте команду [AZ AKS Get-][az-aks-get-upgrades] upgrades. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, Допускается обновление между *1.11. x* -> *1.12. x* или *1.12. x* -> *1.13. x* , однако *1.11. x* -> *1.13. x* — нет.
>
> Для обновления с *1.11. x* -> *1.13. x*сначала выполните обновление с *1.11. x* -> *1.12. x*, а затем выполните обновление с *1.12. x* -> *1.13. x*.

В следующем примере выходных данных показано, что кластер можно обновить до версии *1.12.7* или *1.12.8*.

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

Чтобы обновить список доступных версий для кластера AKS, выполните команду [AZ AKS Upgrade][az-aks-upgrade] . В процессе обновления AKS добавляет в кластер новый узел, который запускает указанную версию Kubernetes, а затем аккуратно [Cordon и][kubernetes-drain] настраивает один из старых узлов, чтобы минимизировать перерывы в работе приложений. Когда новый узел будет подтвержден как работающий модули приложения, старый узел удаляется. Этот процесс повторяется до тех пор, пока не будут обновлены все узлы в кластере.

В следующем примере производится обновление кластера до версии *1.12.8*.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов.

Чтобы убедиться, что обновление прошло успешно, используйте команду [AZ AKS показывать][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластер теперь выполняет *1.12.8*:

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
