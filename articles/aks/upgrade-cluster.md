---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Сведения об обновлении кластера Службы Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621980"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить элементы основного или единого пула узлов по умолчанию в кластере AKS.

Для кластеров AKS, которые используют несколько пулов узлов или узлов Windows Server (в настоящее время в предварительном просмотре в AKS), [см.][nodepool-upgrade]

## <a name="before-you-begin"></a>Перед началом

Эта статья требует, чтобы вы запускали версию Azure CLI 2.0.65 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

> [!WARNING]
> Обновление кластера AKS запускает оцепление и слив узлов. Если у вас есть низкая квота вычислений, обновление может выйти из строя. Подробнее об увеличении квот читайте в [сообщении.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
> Если вы работаете свой собственный кластер аутоскалатор развертывания пожалуйста, отпустите его (вы можете масштабировать его до нуля реплики) во время обновления, как есть шанс, что это будет вмешиваться в процесс обновления. Управляемый автосклер автоматически обрабатывает это. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

С помощью команды [az aks get-upgrades][az-aks-get-upgrades] проверьте выпуски Kubernetes, доступные для кластера. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, допускается обновление между *1.12.x* -> *1.13.x* или *1.13.x* -> *1.14.x,* однако *1.12.x* -> *1.14.x* не является.
>
> Для обновления, от *1.12.x* -> *1.14.x*, первый апгрейд с *1.12.x* -> *1.13.x*, затем обновление с *1.13.x* -> *1.14.x*.

Следующий пример показывает, что кластер может быть повышен до версий *1.13.9* и *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Если обновление не доступно, вы получите:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

С помощью команды [az aks upgrade][az-aks-upgrade] обновите кластер AKS, используя список доступных версий. В процессе обновления AKS добавляет новый узлы в кластер, который выполняет указанную версию Kubernetes, затем тщательно [окорывает и истощает][kubernetes-drain] один из старых узлов, чтобы свести к минимуму сбои в работе приложений. Когда новый узлы подтверждены как запущенные стручки приложения, старый узла удаляется. Этот процесс повторяется до тех пор, пока все узлы в кластере не будут обновлены.

Следующий пример обновляет кластер до версии *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов. 

> [!NOTE]
> Существует общее разрешенное время для завершения обновления кластера. Это время рассчитывается путем `10 minutes * total number of nodes in the cluster`принятия продукта . Например, в кластере 20 узлов операции обновления должны быть успешными за 200 минут, иначе AKS сведет операцию из строя, чтобы избежать невосстановимого состояния кластера. Чтобы восстановиться при сбое обновления, повторите операцию обновления после тайм-аута.

Чтобы проверить, что обновление выполнено успешно, введите команду [az aks show][az-aks-show].

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Следующий пример вывода показывает, что кластер теперь работает *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
