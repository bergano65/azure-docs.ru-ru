---
title: Обновление образов узлов Azure Kubernetes Service (AKS)
description: Узнайте, как обновить образы на узлах кластера AKS и пулах узлов.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 211190228c1ea9c98004b55da96ad38808821d67
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682389"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Обновление образа узла службы Azure Kubernetes Service (AKS)

AKS поддерживает обновление образов на узле, так что вы будете в курсе последних обновлений операционной системы и среды выполнения. AKS предоставляет один новый образ в неделю с последними обновлениями, поэтому рекомендуется регулярно обновлять образы узла для получения последних компонентов, включая обновления Linux или Windows. В этой статье показано, как обновить образы узлов кластера AKS, а также как обновить образы пула узлов без обновления версии Kubernetes.

Если вы заинтересованы в изучении последних образов, предоставляемых AKS, см. Дополнительные сведения в [заметках о выпуске AKS](https://github.com/Azure/AKS/releases) .

Сведения об обновлении версии Kubernetes для кластера см. в статье [Обновление кластера AKS][upgrade-cluster].

## <a name="limitations"></a>Ограничения

* Кластер AKS должен использовать масштабируемые наборы виртуальных машин для узлов.

## <a name="install-the-aks-cli-extension"></a>Установка расширения CLI AKS

Перед выпуском следующей основной версии интерфейса командной строки требуется расширение CLI *AKS-Preview* , чтобы использовать обновление образа узла. Используйте команду [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Обновление всех узлов во всех пулах узлов

Обновление образа узла выполняется с помощью `az aks upgrade` . Чтобы обновить образ узла, используйте следующую команду:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Во время обновления проверьте состояние образов узлов, выполнив следующую `kubectl` команду, чтобы получить метки и отфильтровать сведения о текущем образе узла:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

После завершения обновления используйте `az aks show` для получения обновленных сведений о пуле узлов. Текущий образ узла отображается в `nodeImageVersion` свойстве.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Обновление определенного пула узлов

Обновление образа в пуле узлов аналогично обновлению образа в кластере.

Чтобы обновить образ ОС пула узлов без выполнения обновления кластера Kubernetes, используйте `--node-image-only` параметр в следующем примере:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Во время обновления проверьте состояние образов узлов, выполнив следующую `kubectl` команду, чтобы получить метки и отфильтровать сведения о текущем образе узла:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

После завершения обновления используйте `az aks nodepool show` для получения обновленных сведений о пуле узлов. Текущий образ узла отображается в `nodeImageVersion` свойстве.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Обновление образов узлов с пререзкостью узлов

Чтобы ускорить процесс обновления образа узла, можно обновить образы узлов, используя настраиваемое значение всплеска узлов. По умолчанию AKS использует один дополнительный узел для настройки обновлений.

Если вы хотите увеличить скорость обновления, используйте `--max-surge` значение, чтобы настроить число узлов, используемых для обновления, чтобы они выполнялись быстрее. Дополнительные сведения о компромиссах различных `--max-surge` параметров см. в разделе [Настройка обновления всплесков узлов][max-surge].

Следующая команда задает максимальное значение всплеска для выполнения обновления образа узла:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Во время обновления проверьте состояние образов узлов, выполнив следующую `kubectl` команду, чтобы получить метки и отфильтровать сведения о текущем образе узла:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Используйте `az aks nodepool show` для получения обновленных сведений о пуле узлов. Текущий образ узла отображается в `nodeImageVersion` свойстве.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о последних образах узлов см. в [заметках о выпуске AKS](https://github.com/Azure/AKS/releases) .
- Узнайте, как обновить версию Kubernetes с помощью [обновления кластера AKS][upgrade-cluster].
- [Применение обновлений безопасности и ядра для узлов Linux в службе Kubernetes Azure (AKS)][security-update]
- Узнайте больше о нескольких пулах узлов и об обновлении пулов узлов с помощью [создания нескольких пулов узлов и управления ими][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
