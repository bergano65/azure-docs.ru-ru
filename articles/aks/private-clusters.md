---
title: Создание частного кластера службы Kubernetes Azure
description: Узнайте, как создать частный кластер службы Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 0a05bd15fff97d4f0020f6ce82ee90a2fe995edf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944198"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Создание частного кластера службы Kubernetes Azure (Предварительная версия)

В частном кластере плоскость управления или сервер API имеют внутренние IP-адреса, которые определены в [адресные пространства RFC1918-Addressing для частных документов Интернета](https://tools.ietf.org/html/rfc1918) . С помощью частного кластера можно гарантировать, что сетевой трафик между сервером API и пулами узлов остается только в частной сети.

Плоскость управления или сервер API находятся в подписке Azure, управляемой Azure Kubernetes Service (AKS). Кластер или пул узлов клиента находится в подписке клиента. Сервер и кластер или пул узлов могут взаимодействовать друг с другом через [службу частной связи Azure][private-link-service] в виртуальной сети сервера API и закрытую конечную точку, доступную в подсети кластера AKS клиента.

> [!IMPORTANT]
> Функции предварительной версии AKS являются самостоятельными и предоставляются на основе согласия. Предварительные версии предоставляются *как есть* и *доступны* и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Поэтому функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="prerequisites"></a>предварительные требования

* Azure CLI версия 2.0.77 или более поздняя и версия расширения Azure CLI AKS Preview 0.4.18

## <a name="currently-supported-regions"></a>Поддерживаемые в настоящее время регионы

* Восточная Австралия
* Юго-Восточная часть Австралии
* Южная Бразилия
* Центральная Канада
* Восточная Канада
* Ценрал США
* Восточная Азия
* Восточная часть США
* восточная часть США 2
* Восточная часть США 2 (EUAP)
* Центральная Франция
* Северная Германия
* Восточная Япония
* Западная Япония
* Республика Корея, центральный регион
* Республика Корея, южный регион
* Центрально-северная часть США
* Северная Европа
* Северная Европа
* Центрально-южная часть США
* южная часть Соединенного Королевства
* Западная Европа
* западная часть США
* западная часть США 2
* восточная часть США 2

## <a name="currently-supported-availability-zones"></a>В настоящее время поддерживается Зоны доступности

* Центральная часть США
* Восточная часть США
* восточная часть США 2
* Центральная Франция
* Восточная Япония
* Северная Европа
* Юго-Восточная Азия
* южная часть Соединенного Королевства
* Западная Европа
* западная часть США 2

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Установите последнюю версию расширения Azure CLI AKS Preview

Для использования частных кластеров требуется расширение Azure CLI AKS Preview версии 0.4.18 или более поздней. Установите расширение Azure CLI AKS Preview с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений, выполнив следующую команду [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать параметры по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Чтобы состояние регистрации отображалось как *зарегистрированное*, может потребоваться несколько минут. Проверить состояние можно с помощью следующей команды [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

После регистрации состояния обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью следующей команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
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
* В настоящее время *Служба DNS* не поддерживается.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

