---
title: Авторизованный сервер API ip диапазонов в Azure Kubernetes службы (AKS)
description: Узнайте, как обезопасить кластер с помощью диапазона IP-адресов для доступа к серверу API в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126619"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Безопасный доступ к серверу API с помощью авторизованных диапазонов IP-адресов в службе Azure Kubernetes (AKS)

В Kubernetes сервер API получает запросы на выполнение действий в кластере, например, для создания ресурсов или масштабирования количества узлов. Сервер API является центральным способом взаимодействия с кластером и управления им. Для повышения безопасности кластеров и минимизации атак сервер API должен быть доступен только из ограниченного набора диапазонов IP-адресов.

В этой статье показано, как использовать API сервера авторизованных IP-адресов, чтобы ограничить, какие IP-адреса и CIDRs могут получить доступ к плоскости управления.

> [!IMPORTANT]
> В новых кластерах диапазоны авторизованных IP-адресов сервера API поддерживаются только на балансе нагрузочного баланса *Standard* SKU. Существующие кластеры с балансивизатором нагрузки *Basic* SKU и настраиваемыми диапазонами IP-адресов сервера API будут продолжать работать как есть, но не могут быть перенесены на балансилер нагрузки *Standard* SKU. Эти существующие кластеры также будут продолжать работать, если их версия Kubernetes или плоскость управления будут модернизированы.

## <a name="before-you-begin"></a>Перед началом

Авторизованные диапазоны IP-адресов сервера API работают только для новых кластеров AKS, которые вы создаете. В этой статье показано, как создать кластер AKS с помощью Azure CLI.

Вам нужна версия Azure CLI 2.0.76 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Обзор авторизованных диапазонов IP-адресов сервера API

Сервер API Kubernetes — это то, как подвергаются базовые API Kubernetes. Этот компонент поддерживает взаимодействие со средствами управления, например с `kubectl` или панелью мониторинга Kubernetes. AKS предоставляет мастер кластера с одним арендатором со специальным сервером API. По умолчанию серверу API присваивается общедоступный IP-адрес, и вы должны контролировать доступ с помощью элементов управления доступом на основе ролей (RBAC).

Чтобы обеспечить доступ к общедоступному в противном случае серверу управления AKS/ API, можно включить и использовать авторизованные диапазоны IP. Эти авторизованные диапазоны IP позволяют только определенные диапазоны IP-адресов общаться с сервером API. Запрос, сделанный серверу API с IP-адреса, который не является частью этих авторизованных диапазонов IP, блокируется. Продолжайте использовать RBAC для авторизации пользователей и заданных действий.

Для получения дополнительной информации о сервере API и других компонентах кластера [см.][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Создание кластера AKS с включенным диапазоном IP-адресов сервера API

Авторизованные диапазоны IP-адресов API работают только для новых кластеров AKS. Создайте кластер с помощью [az aks создать][az-aks-create] и указать *--api-сервер-авторизованных-ip-диапазонов* параметр для предоставления списка авторизованных диапазонов IP-адресов. Эти диапазоны IP-адресов обычно являются диапазонами адресов, используемыми вашими сетевыми сетями или общедоступными IP-адресами. Когда вы указываете диапазон CIDR, начните с первого IP-адреса в диапазоне. Например, *137.117.106.90/29* является допустимым диапазоном, но убедитесь, что вы указываете первый IP-адрес в диапазоне, например *137.117.106.88/29.*

> [!IMPORTANT]
> По умолчанию кластер использует [балансизатор нагрузки Standard SKU,][standard-sku-lb] который можно использовать для настройки исходящего шлюза. При включании авторизованных диапазонов IP-адресов сервера API во время создания кластера, общедоступный IP для кластера также разрешен по умолчанию в дополнение к указанным диапазонам. Если вы укажете *""* или нет значения для *--api-сервер-авторизованных-ip-диапазонов,* API сервер авторизованных IP диапазонов будет отключен.

Следующий пример создает кластер с одним узлом под названием *myAKSCluster* в группе ресурсов под названием *myResourceGroup* с включенными диапазонами IP-адресов сервера API. Диапазоны IP-адресов *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Эти диапазоны следует добавить в список разрешений:
> - Общедоступный IP-адрес брандмауэра
> - Любой диапазон, представляющий сети, которые вы будете управлять кластером из
> - Если вы используете пространства Azure Dev в кластере AKS, необходимо разрешить [дополнительные диапазоны в зависимости от региона.][dev-spaces-ranges]

> Верхний предел для числа диапазонов IP, которые вы можете указать, составляет 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Укажите исходящие ИП для балансиваля нагрузки Standard SKU

При создании кластера AKS, если вы указываете исходящие IP-адреса или префиксы для кластера, эти адреса или префиксы также разрешены. Пример:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

В приведенном выше примере все IP-адреса, предусмотренные в параметре *- load-balancer-outbound-ip-префиксы,* допускаются вместе с *IP-ip-параметрами --api-server-server-ip-диапазонов.*

Кроме того, можно указать параметр IP-префикса ip-префикса иссусу исправившегося баланса нагрузки *- нагрузка-баланс-баланс-префиксы.*

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Разрешить только исходящий публичный IP балансиста нагрузки Standard SKU

При включании авторизованных диапазонов IP-адресов сервера API во время создания кластера, исходящий публичный IP для балансиваля нагрузки Standard SKU для вашего кластера также допускается по умолчанию в дополнение к указанным диапазонам. Чтобы разрешить только исходящий публичный IP балансиста нагрузки Standard SKU, используйте *0.0.0.0.0/32* при указании параметра *--api-server-авторизованный-ip-диапазонов.*

В следующем примере допускается только исходящий публичный IP-равноправия нагрузки Standard SKU, и доступ к серверу API можно получить только из узлов в кластере.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Обновление диапазонов IP-адресов сервера API, авторизованного в кластере

Для обновления диапазонов IP-адресов сервера API, авторизованных IP-диапазонов на существующем кластере, используйте команду [обновления az aks][az-aks-update] и используйте *параметры --api-server-authorized-ip-диапазоны*, *--load-balancer-outbound-ip-ip-prefixes.* *--load-balancer-outbound-ip-prefixes* *--load-balancer-outbound-ip-prefixes*

Следующий пример обновляет API сервер авторизованных диапазонов IP на кластере под названием *myAKSCluster* в группе ресурсов под названием *myResourceGroup*. Диапазон IP-адресов для авторизации *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Вы также можете использовать *0.0.0.0/32* при указании *параметра --api-сервер-авторизованный-ip-диапазонов,* чтобы позволить только публичный IP балансиватель нагрузки Standard SKU.

## <a name="disable-authorized-ip-ranges"></a>Отключить авторизованные диапазоны Ip

Чтобы отключить авторизованные диапазоны IP, используйте [обновление az aks][az-aks-update] и укажите пустой диапазон для отстранить от сервера API авторизованные диапазоны IP. Пример:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы включили авторизованные диапазоны IP-адресов сервера API. Этот подход является частью того, как можно запустить безопасный кластер AKS.

Для получения дополнительной информации см. [концепции безопасности для приложений и кластеров в AKS][concepts-security] и [рекомендации по безопасности кластеров и обновлениям в AKS.][operator-best-practices-cluster-security]

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
