---
title: Масштабирование кластера службы Azure Kubernetes
description: Сведения о том, как масштабировать число узлов в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472595"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Масштабирование числа узлов в кластере Службы Azure Kubernetes (AKS)

Если ресурсы требуют изменений приложений, вы можете вручную масштабировать кластер AKS, чтобы запустить другое число узлов. При уменьшении масштаба узлы тщательно [блокируются и][kubernetes-drain] останавливаются, чтобы минимизировать перерывы в работе приложений. При увеличении масштаба AKS ожидает, пока узлы не помечаются `Ready` кластером Kubernetes до планирования модулей массовой загрузки.

## <a name="scale-the-cluster-nodes"></a>Масштабирование узлов кластера

Сначала получите *имя* пула узлов с помощью команды [AZ AKS показывать][az-aks-show] . В следующем примере показано получение имени пула узлов для кластера с именем *myAKSCluster* в группе ресурсов *myResourceGroup* :

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

Используйте команду [AZ AKS Scale][az-aks-scale] для масштабирования узлов кластера. В следующем примере кластер *myAKSCluster* масштабируется до одного узла. Укажите собственное значение *--nodepool-name* из предыдущей команды, например *nodepool1*.

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

В этой статье вы вручную масштабируете кластер AKS, чтобы увеличить или уменьшить количество узлов. Вы также можете использовать [Автомасштабирование кластера][cluster-autoscaler] для автоматического масштабирования кластера.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
