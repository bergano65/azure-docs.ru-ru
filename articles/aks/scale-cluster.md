---
title: Масштабирование кластера службы Azure Kubernetes
description: Сведения о том, как масштабировать число узлов в кластере Службы Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: d5686a74ffe138af51d2319c839a3a5c5887f992
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902943"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Масштабирование числа узлов в кластере Службы Azure Kubernetes (AKS)

Если ресурсы требуют изменений приложений, вы можете вручную масштабировать кластер AKS, чтобы запустить другое число узлов. При уменьшении масштаба узлы будут тщательно [заблокированы и остановлены][kubernetes-drain], чтобы свести к минимуму время простоя работающих приложений. При увеличении масштаба AKS ожидает, пока узлы не помечаются `Ready` кластером Kubernetes, прежде чем они будут запланированы для них.

## <a name="scale-the-cluster-nodes"></a>Масштабирование узлов кластера

Сначала получите *имя* пула узлов с помощью команды [AZ AKS показывать][az-aks-show] . В следующем примере показано получение имени пула узлов для кластера с именем *myAKSCluster* в группе ресурсов *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

В следующем примере выходных данных *name* равно *nodepool1*.

```output
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

Используйте команду [AZ AKS Scale][az-aks-scale] для масштабирования узлов кластера. В следующем примере кластер *myAKSCluster* масштабируется до одного узла. Укажите собственную `--nodepool-name` из предыдущей команды, например *nodepool1*:

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


## <a name="scale-user-node-pools-to-0"></a>Масштабирование `User` пулов узлов до 0

В отличие от `System` пулов узлов, которым всегда требуются выполняющиеся узлы, `User` Пулы узлов позволяют масштабировать их до 0. Дополнительные сведения о различиях между пулами системных и пользовательских узлов см. в разделе [пулы системных и пользовательских](use-system-pools.md)узлов.

Чтобы масштабировать пул пользователей до 0, можно воспользоваться командой [AZ AKS нодепул Scale][az-aks-nodepool-scale] в качестве альтернативы приведенной выше `az aks scale` команде и задать 0 в качестве числа узлов.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Можно также автоматически масштабировать `User` Пулы узлов до 0 узлов, установив `--min-count` для параметра [автомасштабирования кластера](cluster-autoscaler.md) значение 0.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы вручную масштабируете кластер AKS, чтобы увеличить или уменьшить количество узлов. Вы также можете использовать [Автомасштабирование кластера][cluster-autoscaler] для автоматического масштабирования кластера.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale&preserve-view=true