---
title: IP-диапазоны авторизации сервера API в службе Kubernetes Azure (AKS)
description: Узнайте, как защитить кластер с помощью диапазона IP-адресов для доступа к серверу API в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: 6fc1af356d035c4db73f761ce679f7ad16126d4f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013013"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Безопасный доступ к серверу API с помощью диапазонов полномочных IP-адресов в службе Kubernetes Azure (AKS)

В Kubernetes сервер API получает запросы на выполнение действий в кластере, например для создания ресурсов или масштабирования количества узлов. Сервер API является основным способом взаимодействия с кластером и управления им. Чтобы повысить безопасность кластера и сократить число атак, сервер API должен быть доступен только из ограниченного набора диапазонов IP-адресов.

В этой статье показано, как использовать диапазоны разрешенных IP-адресов сервера API для ограничения IP-адресов и Цидрс для доступа к плоскости управления.

> [!IMPORTANT]
> В новых кластерах разрешенные диапазоны IP-адресов сервера API поддерживаются только в подсистеме балансировки нагрузки уровня " *стандартный* ". Существующие кластеры с подсистемой балансировки нагрузки " *базовый* " и ПОЛНОМОЧНЫЙ IP-адрес сервера API будут продолжать работать, как есть. Эти существующие кластеры также можно обновить, и они будут продолжать работать.

## <a name="before-you-begin"></a>Перед началом работы

Допустимые диапазоны IP-адресов сервера API работают только для создаваемых кластеров AKS. В этой статье показано, как создать кластер AKS с помощью Azure CLI.

Требуется Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Общие сведения о IP-адресах полномочных серверов API

Сервер API Kubernetes — это то, как предоставляются базовые API-интерфейсы Kubernetes. Этот компонент поддерживает взаимодействие со средствами управления, например с `kubectl` или панелью мониторинга Kubernetes. AKS предоставляет главный кластер с одним клиентом с выделенным сервером API. По умолчанию серверу API назначается общедоступный IP-адрес, и управление доступом осуществляется с помощью управления доступом на основе ролей (RBAC).

Чтобы защитить доступ к общедоступной плоскости управления AKS или серверу API, можно включить и использовать разрешенные IP-диапазоны. Эти диапазоны разрешенных IP-адресов разрешают обмен данными только с сервером API. Запрос к серверу API из IP-адреса, который не является частью этих разрешенных диапазонов IP-адресов, блокируется. Продолжайте использовать RBAC для авторизации пользователей и действий, которые они запрашивают.

Дополнительные сведения о сервере API и других компонентах кластера см. в разделе [Основные понятия Kubernetes Core для AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Создание кластера AKS с включенными IP-диапазонами разрешенных серверов API

Допустимые диапазоны IP-адресов сервера API работают только для новых кластеров AKS. Создайте кластер с помощью команды [AZ AKS Create][az-aks-create] и укажите параметр *--API-Server-авторизовать-IP-ranges* , чтобы предоставить список ДОПУСТИМЫХ диапазонов IP-адресов. Эти диапазоны IP-адресов обычно являются диапазонами адресов, используемыми локальными сетями или общедоступными IP-адресами. При указании диапазона CIDR начинается с первого IP-адреса в диапазоне. Например, *137.117.106.90/29* является допустимым диапазоном, но убедитесь, что вы указали первый IP-адрес в диапазоне, например *137.117.106.88/29*.

> [!IMPORTANT]
> По умолчанию в кластере используется [балансировщик нагрузки стандартного SKU][standard-sku-lb] , который можно использовать для настройки исходящего шлюза. При включении разрешенных диапазонов IP-адресов сервера API во время создания кластера общедоступный IP-адрес кластера также разрешается по умолчанию в дополнение к указанным диапазонам. Если указать значение " *"* или не указывать значения для параметра *--API-Server-авторизовать-IP-ranges*, то диапазоны разрешенных IP-адресов сервера API будут отключены.

В следующем примере создается кластер с одним узлом с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup* с включенными диапазонами разрешенных IP-адресов сервера API. Допустимые диапазоны IP-адресов: *73.140.245.0/24*.

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
> - Любой диапазон, представляющий сети, из которых будет осуществляться администрирование кластера.
> - Если вы используете Azure Dev Spaces в кластере AKS, необходимо разрешить [Дополнительные диапазоны на основе вашего региона][dev-spaces-ranges].

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Укажите исходящие IP-адреса для стандартного балансировщика нагрузки SKU

При создании кластера AKS, если указаны исходящие IP-адреса или префиксы для кластера, эти адреса или префиксы также разрешены. Например,

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

В приведенном выше примере все IP-адреса, указанные в параметре *--Load-балансировщик-Outbound-IP* , разрешены вместе с IP-адресами в параметре *— API-Server-Allowed-IP-ranges* .

Кроме того, можно указать параметр *--Load-балансировщик-Outbound-IP-префиксы* , чтобы разрешить исходящие префиксы IP-адресов балансировки нагрузки.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Разрешить только исходящий общедоступный IP-адрес SKU "Стандартный" балансировщика нагрузки

При включении допустимых диапазонов IP-адресов для сервера API во время создания кластера в дополнение к указанным диапазонам также разрешены исходящие общедоступные IP-адреса для стандартного балансировщика нагрузки SKU для кластера. Чтобы разрешить только исходящий общедоступный IP-адрес для балансировщика нагрузки уровня "Стандартный", используйте *0.0.0.0/32* при указании параметра *--API-Server-Allowed-IP-ranges* .

В следующем примере разрешен только исходящий общедоступный IP-адрес подсистемы балансировки нагрузки уровня "Стандартный", и доступ к серверу API можно получить только с узлов в кластере.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Обновление диапазонов IP-адресов сервера API кластера

Чтобы обновить разрешенные IP-адреса сервера API в существующем кластере, выполните команду [AZ AKS Update][az-aks-update] и используйте командлеты *--API-Server-авторизовать-IP-ranges*, *--Load-балансировщик-Outbound-IP-префиксы*, *--Load-балансировщик-Outbound-* Servers, или *--Load-балансировщик-Outbound-IP — префиксы* параметров.

В следующем примере выполняется обновление диапазонов IP-адресов сервера API в кластере с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*. Диапазон IP-адресов для авторизации — *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Можно также использовать *0.0.0.0/32* при указании параметра *--API-Server-Allowed-IP-ranges* , чтобы разрешить только общедоступный IP-адрес стандартного балансировщика нагрузки SKU.

## <a name="disable-authorized-ip-ranges"></a>Отключение диапазонов IP-адресов с правом доступа

Чтобы отключить допустимые диапазоны IP-адресов, выполните команду [AZ AKS Update][az-aks-update] и укажите пустой диапазон, чтобы отключить полномочные IP-адреса сервера API. Например,

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы включили IP-диапазоны разрешенных серверов API. Этот подход является одной из частей того, как можно запустить защищенный кластер AKS.

Дополнительные сведения см. в статьях [Основные понятия безопасности для приложений и кластеров в AKS][concepts-security] и рекомендации [по обеспечению безопасности и обновлениям кластера в AKS][operator-best-practices-cluster-security].

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
