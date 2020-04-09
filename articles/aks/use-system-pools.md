---
title: Использование системных узлов в службе Azure Kubernetes (AKS)
description: Узнайте, как создавать и управлять пулами системных узлов в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: ef5400f19f68fd2da45776d220e17777f58e46e6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986321"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Управляйте пулами системных узлов в службе Azure Kubernetes (AKS)

В службе Azure Kubernetes (AKS) узлы одной и той же конфигурации сгруппированы в *пулы узлов.* Пулы узлов содержат базовые VMs, которые управляют приложениями. Пулы системных узлов и пулы узлов пользователя представляют два различных режима пула узлов для кластеров AKS. Системные узлы объединяют основные системные службы, такие как CoreDNS. Пулы узлов пользователей — это место, где размещаются стручки, специфичные для приложений. Каждый кластер AKS должен содержать по крайней мере один пул системных узлов с по крайней мере одним узлами. Если вы используете единый пул узлов системы для кластера AKS, мы рекомендуем использовать по крайней мере три узла для пула узлов. 

## <a name="before-you-begin"></a>Подготовка к работе

* Вам нужна версия Azure CLI 2.3.1 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="limitations"></a>Ограничения

При создании и управлении кластерами AKS, поддерживающих пулы узлов системы, применяются следующие ограничения.

* См [Квоты, ограничения размера виртуальных машин и доступность региона в службе Azure Kubernetes (AKS)][quotas-skus-regions].
* Кластер AKS должен быть построен с виртуальными наборами масштабов машин в качестве типа VM.
* Название пула узлов может содержать только буквенные символы нижнего регистра и должно начинаться с буквы нижнего регистра. Для пулов узлов Linux длина должна быть от 1 до 12 символов. Для пулов узлов Windows длина должна быть от 1 до 6 символов.

## <a name="system-and-user-node-pools"></a>Пулы системных и узлов пользователей

Узлы пула системного узла имеют метку **kubernetes.azure.com/mode: система.** Каждый кластер AKS содержит по крайней мере один пул системных узлов. Пулы системных узлов имеют следующие ограничения:

* Система бассейнов osType должна быть Linux.
* Пулы узлов пользователей osType могут быть Linux или Windows.
* Системные пулы должны содержать по крайней мере один узлы, а пулы узлов пользователя могут содержать ноль или несколько узлов.
* Системные пулы узлов требуют VM SKU, по крайней мере 2 vCPUs и 4 ГБ памяти.
* Пулы системных узлов должны поддерживать не менее 30 стручков, как описано [в формуле минимального и максимального значения для стручков.][maximum-pods]
* Пулы spot-узлов требуют пулов узлов пользователя.

Вы можете сделать следующие операции с пулами узлов:

* Измените пул узлов системы, чтобы он был пулом узлов пользователя, при условии, что у вас есть другой пул узлов системы, чтобы занять свое место в кластере AKS.
* Измените пул узлов пользователя в пул системных узлов.
* Удаление пулов узлов пользователя.
* Вы можете удалить пулы системных узлов, при условии, что у вас есть другой пул системных узлов, чтобы занять свое место в кластере AKS.
* Кластер AKS может иметь несколько пулов системных узлов и требует, по крайней мере, одного пула системных узлов.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Создание нового кластера AKS с пулом системных узлов

При создании нового кластера AKS автоматически создается пул системных узлов с одним узлами. Первоначальный пул узлов по умолчанию перестоит режиму системы типа. При создании новых пулов узлов с добавлением az aks nodepool эти пулы узлов являются пулами узлов пользователя, если только вы явно не укажете параметр режима.

Следующий пример создает группу ресурсов под названием *myResourceGroup* в *восточном* регионе.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS. Следующий пример создает кластер под названием *myAKSCluster* с одним системным пулом, содержащим один узла. Для рабочих нагрузок производства убедитесь, что вы используете пулы системных узлов, по крайней мере, с тремя узлами. Выполнение этой операции займет несколько минут.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Добавление пула системных узлов в существующий кластер AKS

Можно добавить один или несколько пулов системных узлов к существующим кластерам AKS. Следующая команда добавляет пул узлов системы типа режима с количеством по умолчанию трех узлов.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Показать детали для пула узлов

Вы можете проверить детали пула узлов со следующей командой.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Режим **системы** типов определяется для пулов системных узлов, а для пулов узлов пользователя **—** режим типа пользователя.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Обновление системы и пулов узлов пользователя

Режимы могут изменять как для системных, так и для пулов узлов пользователя. Пул узлов системы можно изменить в пул пользователей только в том случае, если в кластере AKS уже существует другой пул узлов системы.

Эта команда изменяет пул узлов системы в пул узлов пользователя.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Эта команда изменяет пул узлов пользователя в пул системных узлов.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Удаление пула системных узлов

> [!Note]
> Чтобы использовать пулы системных узлов на кластерах AKS перед версией API 2020-03-02, добавьте новый пул узлов системы, а затем удалите исходный пул узлов по умолчанию.

Ранее нельзя было удалить пул узлов системы, который был первым пулом узлов по умолчанию в кластере AKS. Теперь у вас есть возможность удалить любой пул узлов из кластеров. Поскольку кластеры AKS требуют по крайней мере одного пула системных узлов, необходимо иметь по крайней мере два пула системных узлов в кластере AKS, прежде чем можно удалить один из них.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как создавать и управлять пулами системных узлов в кластере AKS. Для получения дополнительной информации о том, как использовать несколько пулов узлов, [см.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30