---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Сведения об обновлении кластера Службы Azure Kubernetes (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028446"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как выполнить обновление существующего кластера AKS.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей требуется Azure CLI 2.0.56 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

С помощью команды [az aks get-upgrades][az-aks-get-upgrades] проверьте выпуски Kubernetes, доступные для кластера. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, разрешены обновления между версиями *1.10.x* -> *1.11.x* или *1.11.x* -> *1.12.x*, но *1.10.x* -> *1.12.x* запрещено.
>
> Чтобы выполнить обновление с *1.10.x* -> *1.12.x*, сначала обновите с *1.10.x* -> *1.11.x*, а затем — с *1.11.x* -> *1.12.x*.

В следующем примере выходных данных показано кластер, который можно обновить до версии *1.11.5* или *1.11.6*.

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

С помощью команды [az aks upgrade][az-aks-upgrade] обновите кластер AKS, используя список доступных версий. Чтобы свернуть сбой выполнения приложений, в ходе процесса обновления AKS добавит новый узел в кластер, а затем узлы будут по очереди [блокироваться и останавливаться][kubernetes-drain]. В следующем примере кластер обновляется до версии *1.11.6*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов.

Чтобы проверить, что обновление выполнено успешно, введите команду [az aks show][az-aks-show].

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных видно, что версия кластера — *1.11.6*.

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было показано, как выполнить обновление существующего кластера AKS. Дополнительные сведения о развертывании AKS и управлении ею см. в следующей статье.

> [!div class="nextstepaction"]
> [Руководство. Подготовка приложения для Службы Azure Kubernetes (AKS)][aks-tutorial-prepare-app].

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show