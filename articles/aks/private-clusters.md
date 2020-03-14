---
title: Создание частного кластера службы Kubernetes Azure
description: Узнайте, как создать частный кластер службы Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205168"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Создание частного кластера службы Kubernetes Azure

В частном кластере плоскость управления или сервер API имеют внутренние IP-адреса, которые определены в [адресные пространства RFC1918-Addressing для частных документов Интернета](https://tools.ietf.org/html/rfc1918) . С помощью частного кластера можно гарантировать, что сетевой трафик между сервером API и пулами узлов остается только в частной сети.

Плоскость управления или сервер API находятся в подписке Azure, управляемой Azure Kubernetes Service (AKS). Кластер или пул узлов клиента находится в подписке клиента. Сервер и кластер или пул узлов могут взаимодействовать друг с другом через [службу частной связи Azure][private-link-service] в виртуальной сети сервера API и закрытую конечную точку, доступную в подсети кластера AKS клиента.

## <a name="prerequisites"></a>предварительные требования

* Azure CLI версии 2.2.0 или более поздней.

## <a name="create-a-private-aks-cluster"></a>Создание частного кластера AKS

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов или используйте существующую группу ресурсов для кластера AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Основные сетевые подключения по умолчанию 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Где *--Enable-частный-Cluster* является обязательным флагом для частного кластера. 

### <a name="advanced-networking"></a>Сетевое взаимодействие уровня "Расширенный"  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Где *--Enable-частный-Cluster* является обязательным флагом для частного кластера. 

> [!NOTE]
> Если адрес моста DOCKER (172.17.0.1/16) конфликтует с подсетью CIDR, измените адрес моста DOCKER соответствующим образом.

## <a name="options-for-connecting-to-the-private-cluster"></a>Варианты подключения к частному кластеру

Конечная точка сервера API не имеет общедоступного IP-адреса. Для управления сервером API необходимо использовать виртуальную машину, имеющую доступ к виртуальной сети Azure кластера AKS. Существует несколько вариантов установки сетевого подключения к частному кластеру.

* Создайте виртуальную машину в той же виртуальной сети Azure, что и кластер AKS.
* Используйте виртуальную машину в отдельной сети и настройте [пиринг виртуальных сетей][virtual-network-peering].  Дополнительные сведения об этом параметре см. в разделе ниже.
* Используйте [Express Route или VPN-][express-route-or-VPN] подключение.

Самым простым вариантом является создание виртуальной машины в той же виртуальной сети, что и кластер AKS.  В Express Route и VPN добавляются затраты и требуется дополнительная сложность сети.  Для пиринга виртуальных сетей необходимо спланировать диапазоны CIDR сети, чтобы убедиться в отсутствии перекрывающихся диапазонов.

## <a name="virtual-network-peering"></a>Пиринг между виртуальными сетями

Как уже упоминалось, пиринг виртуальной сети — это один из способов доступа к частному кластеру. Чтобы использовать пиринг виртуальных сетей, необходимо настроить связь между виртуальной сетью и частной зоной DNS.
    
1. Перейдите в группу ресурсов MC_ * в портал Azure.  
2. Выберите частную зону DNS.   
3. На левой панели выберите ссылку **Виртуальная сеть** .  
4. Создайте новую ссылку, чтобы добавить виртуальную сеть виртуальной машины в частную зону DNS. Ссылка на зону DNS займет несколько минут.  
5. Вернитесь к группе ресурсов MC_ * в портал Azure.  
6. В области справа выберите виртуальную сеть. Имя виртуальной сети указано в формате *AKS-vnet-\** .  
7. В левой области выберите **пирингs (пиринга**).  
8. Выберите **Добавить**, добавьте виртуальную сеть виртуальной машины, а затем создайте пиринг.  
9. Перейдите в виртуальную сеть с виртуальной машиной, выберите **пиринг**, выберите виртуальную сеть AKS, а затем создайте пиринг. Если диапазоны адресов в виртуальной сети AKS и конфликт виртуальной сети виртуальной машины, пиринг завершится сбоем. Дополнительные сведения см. в разделе [пиринг виртуальных сетей][virtual-network-peering].

## <a name="dependencies"></a>Зависимости  

* Служба Private Link поддерживается только для стандартных Azure Load Balancer. Базовый Azure Load Balancer не поддерживается.  
* Чтобы использовать настраиваемый DNS-сервер, разверните сервер AD с DNS для пересылки в этот IP-168.63.129.16.

## <a name="limitations"></a>Ограничения 
* Диапазоны IP-адресов не могут быть применены к конечной точке сервера закрытого API, они применяются только к общедоступному серверу API.
* Зоны доступности в настоящее время поддерживаются в определенных регионах, см. начало этого документа. 
* [Ограничения службы частной связи Azure][private-link-service] применяются к частным кластерам, частным конечным точкам Azure и конечным точкам службы виртуальной сети, которые в настоящее время не поддерживаются в одной виртуальной сети.
* Нет поддержки виртуальных узлов в частном кластере для прокрутки закрытых экземпляров контейнеров Azure (ACI) в частной виртуальной сети Azure.
* Без поддержки интеграции Azure DevOps из Box с частными кластерами
* Для клиентов, которым необходимо включить реестр контейнеров Azure для работы с частными AKS, виртуальная сеть реестра контейнеров должна быть соединена с виртуальной сетью кластера агента.
* Нет текущей поддержки для Azure Dev Spaces
* Нет поддержки преобразования существующих кластеров AKS в частные кластеры
* Удаление или изменение частной конечной точки в подсети клиента приведет к прекращению работы кластера. 
* Azure Monitor for Containers Live Data в настоящее время не поддерживается.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

