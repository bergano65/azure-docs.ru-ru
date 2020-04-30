---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Узнайте, как обновить кластер Azure Kubernetes Service (AKS), чтобы получить новейшие функции и обновления для системы безопасности.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 7e9a47b7bda4cdb0ff6f1983bc884f7441a26d9b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207978"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить главные компоненты или один пул узлов по умолчанию в кластере AKS.

Для кластеров AKS, использующих несколько пулов узлов, см. статью [Обновление пула узлов в AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Подготовка к работе

Для работы с этой статьей требуется Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

> [!WARNING]
> Обновление кластера AKS активирует Cordon и сток узлов. При наличии свободной квоты вычислений обновление может завершиться ошибкой. Дополнительные сведения см. в разделе [увеличение квот](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
> Если вы используете собственное развертывание автомасштабирования кластера, отключите его (вы можете масштабировать его до нулевых реплик) во время обновления, так как это может помешать процессу обновления. Управляемый Автомасштабирование автоматически обрабатывает это. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

С помощью команды [az aks get-upgrades][az-aks-get-upgrades] проверьте выпуски Kubernetes, доступные для кластера. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, Допускается обновление с параметром *1.12. x* -> *1.13. x* или *1.13. x* -> *1.14. x* , однако *1.12. x* -> *1.14. x* — нет.
>
> Чтобы выполнить обновление, начиная с *1.12. x* -> *1.14. x*, сначала выполните обновление с *1.12. x* -> *1.13. x*, а затем выполните обновление с *1.13. x* -> *1.14. x*.

В следующем примере выходных данных показано, что кластер можно обновить до версии *1.13.9* и *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Если обновление недоступно, вы получите следующее:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

С помощью команды [az aks upgrade][az-aks-upgrade] обновите кластер AKS, используя список доступных версий. В процессе обновления AKS добавляет в кластер новый узел, который запускает указанную версию Kubernetes, а затем аккуратно [Cordon и][kubernetes-drain] настраивает один из старых узлов, чтобы минимизировать перерывы в работе приложений. Когда новый узел будет подтвержден как работающий модули приложения, старый узел удаляется. Этот процесс повторяется до тех пор, пока не будут обновлены все узлы в кластере.

В следующем примере производится обновление кластера до версии *1.13.10*.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов. 

> [!NOTE]
> Существует общее допустимое время для завершения обновления кластера. Это время вычисляется с помощью продукта `10 minutes * total number of nodes in the cluster`. Например, в кластере из 20 узлов операции обновления должны успешно пройти через 200 минут, иначе AKS не сможет выполнить операцию, чтобы избежать неисправимого состояния кластера. Чтобы восстановиться при сбое обновления, повторите операцию обновления после того, как будет достигнуто время ожидания.

Чтобы проверить, что обновление выполнено успешно, введите команду [az aks show][az-aks-show].

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластер теперь выполняет *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дальнейшие шаги

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
