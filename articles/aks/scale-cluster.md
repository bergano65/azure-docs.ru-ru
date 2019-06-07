---
title: Масштабирование кластера службы Azure Kubernetes
description: Сведения о том, как масштабировать число узлов в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475132"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Масштабирование числа узлов в кластере Службы Azure Kubernetes (AKS)

Если ресурсы требуют изменений приложений, вы можете вручную масштабировать кластер AKS, чтобы запустить другое число узлов. При уменьшении масштаба узлы будут тщательно [заблокированы и остановлены][kubernetes-drain], чтобы свести к минимуму время простоя работающих приложений. При увеличении масштаба, AKS ожидает узлы будут отмечены `Ready` кластер Kubernetes, прежде чем модулей запланировано на их.

## <a name="scale-the-cluster-nodes"></a>Масштабирование узлов кластера

Во-первых, получите *имя* пула узла с помощью [az aks show] [ az-aks-show] команды. В следующем примере возвращается имя группы узлов для кластера с именем *myAKSCluster* в *myResourceGroup* группы ресурсов:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

В следующем примере выходных данных *name* равно *nodepool1*.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Используйте [az aks масштабирования] [ az-aks-scale] команду, чтобы масштабировать узлы кластера. В следующем примере кластер *myAKSCluster* масштабируется до одного узла. Укажите собственное значение *--nodepool-name* из предыдущей команды, например *nodepool1*.

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

В следующем примере выходных данных показано, что кластер успешно масштабирован до одного узла, как показано в разделе *agentPoolProfiles*.

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы вручную масштабировать кластер AKS, чтобы увеличить или уменьшить количество узлов. Можно также использовать [кластера автомасштабирования] [ cluster-autoscaler] (сейчас в предварительной версии в AKS) автоматически масштабировать кластер.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
