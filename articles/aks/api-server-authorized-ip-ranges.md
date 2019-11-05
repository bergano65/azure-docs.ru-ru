---
title: IP-диапазоны авторизации сервера API в службе Kubernetes Azure (AKS)
description: Узнайте, как защитить кластер с помощью диапазона IP-адресов для доступа к серверу API в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472971"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Безопасный доступ к серверу API с помощью диапазонов полномочных IP-адресов в службе Kubernetes Azure (AKS)

В Kubernetes сервер API получает запросы на выполнение действий в кластере, например для создания ресурсов или масштабирования количества узлов. Сервер API является основным способом взаимодействия с кластером и управления им. Чтобы повысить безопасность кластера и сократить число атак, сервер API должен быть доступен только из ограниченного набора диапазонов IP-адресов.

В этой статье показано, как использовать диапазоны разрешенных IP-адресов сервера API для ограничения IP-адресов и Цидрс для доступа к плоскости управления.

> [!IMPORTANT]
> В новых кластерах разрешенные диапазоны IP-адресов сервера API поддерживаются только в подсистеме балансировки нагрузки уровня " *стандартный* ". Существующие кластеры с подсистемой балансировки нагрузки " *базовый* " и ПОЛНОМОЧНЫЙ IP-адрес сервера API будут продолжать работать, как есть. Эти существующие клусерс также могут быть обновлены, и они будут продолжать работать.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что вы работаете с кластерами, использующими [кубенет][kubenet].  В кластерах, основанных на [контейнерах Azure (CNI)][cni-networking] , отсутствует нужная таблица маршрутов для защиты доступа.  Таблицу маршрутов потребуется создать вручную.  Дополнительные сведения см. в разделе [Управление таблицами маршрутов](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Допустимые диапазоны IP-адресов сервера API работают только для создаваемых кластеров AKS. В этой статье показано, как создать кластер AKS с помощью Azure CLI.

Требуется Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

## <a name="limitations"></a>Ограничения

При настройке допустимых диапазонов IP-адресов сервера API применяются следующие ограничения.

* В настоящее время нельзя использовать Azure Dev Spaces, так как связь с сервером API также заблокирована.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Общие сведения о IP-адресах полномочных серверов API

Сервер API Kubernetes — это то, как предоставляются базовые API-интерфейсы Kubernetes. Этот компонент поддерживает взаимодействие со средствами управления, например с `kubectl` или панелью мониторинга Kubernetes. AKS предоставляет главный кластер с одним клиентом с выделенным сервером API. По умолчанию серверу API назначается общедоступный IP-адрес, и управление доступом осуществляется с помощью управления доступом на основе ролей (RBAC).

Чтобы защитить доступ к общедоступной плоскости управления AKS или серверу API, можно включить и использовать разрешенные IP-диапазоны. Эти диапазоны разрешенных IP-адресов разрешают обмен данными только с сервером API. Запрос к серверу API из IP-адреса, который не является частью этих разрешенных диапазонов IP-адресов, блокируется. Следует продолжать использовать RBAC для авторизации пользователей и действий, которые они запрашивают.

Дополнительные сведения о сервере API и других компонентах кластера см. в разделе [Основные понятия Kubernetes Core для AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

Допустимые диапазоны IP-адресов сервера API работают только для новых кластеров AKS. Вы не можете включить диапазоны разрешенных IP-адресов в рамках операции создания кластера. При попытке включить разрешенные диапазоны IP-адресов в процессе создания кластера узлы кластера не смогут получить доступ к серверу API во время развертывания, так как IP-адрес исходящего трафика не определен в этой точке.

Сначала создайте кластер с помощью команды [AZ AKS Create][az-aks-create] . В следующем примере создается кластер с одним узлом с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Обновление кластера с санкционированными IP-адресами

По умолчанию в кластере используется [балансировщик нагрузки "Стандартный" SKU][standard-sku-lb], который можно использовать для настройки исходящего шлюза. Используйте команду [AZ Network public-IP List][az-network-public-ip-list] и укажите группу ресурсов кластера AKS, которая обычно начинается с *MC_* . Отобразится общедоступный IP-адрес кластера, который можно разрешить. Используйте команду [AZ AKS Update][az-aks-update] и укажите параметр *--API-Server-Allowed-IP-ranges* , чтобы разрешить IP-адрес кластера. Например:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Чтобы включить диапазоны разрешенных IP-адресов сервера API, выполните команду [AZ AKS Update][az-aks-update] и укажите параметр *--API-Server-авторизовать-IP-ranges* , чтобы предоставить список РАЗРЕШЕНных диапазонов IP-адреса. Эти диапазоны IP-адресов обычно являются диапазонами адресов, используемыми локальными сетями или общедоступными IP-адресами. При указании диапазона CIDR начинается с первого IP-адреса в диапазоне. Например, *137.117.106.90/29* является допустимым диапазоном, но убедитесь, что вы указали первый IP-адрес в диапазоне, например *137.117.106.88/29*.

В следующем примере включается авторизация IP-адресов сервера API в кластере с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*. Диапазоны IP-адресов для авторизации: *172.0.0.0/16* (диапазон адресов Pod/nodes) и *168.10.0.0/18* (сервицеЦидр):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Эти диапазоны следует добавить в список разрешений:
> - Общедоступный IP-адрес брандмауэра
> - CIDR службы
> - Диапазон адресов для подсетей с использованием узлов и модулей Pod
> - Любой диапазон, представляющий сети, из которых будет осуществляться администрирование кластера.

## <a name="disable-authorized-ip-ranges"></a>Отключение диапазонов IP-адресов с правом доступа

Чтобы отключить допустимые диапазоны IP-адресов, выполните команду [AZ AKS Update][az-aks-update] и укажите пустой диапазон, чтобы отключить полномочные IP-адреса сервера API. Например:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы включили IP-диапазоны разрешенных серверов API. Этот подход является одной из частей того, как можно запустить защищенный кластер AKS.

Дополнительные сведения см. в статьях [Основные понятия безопасности для приложений и кластеров в AKS][concepts-security] и рекомендации [по обеспечению безопасности и обновлениям кластера в AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
