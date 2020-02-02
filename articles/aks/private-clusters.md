---
title: Создание частного кластера службы Kubernetes Azure
description: Узнайте, как создать частный кластер службы Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: 934dfdb43d6d2e4ccc346b728f0ac4f5febea327
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932596"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Создание частного кластера службы Kubernetes Azure (Предварительная версия)

В частном кластере плоскость управления или сервер API имеют внутренние IP-адреса, которые определены в [адресные пространства RFC1918-Addressing для частных документов Интернета](https://tools.ietf.org/html/rfc1918) . С помощью частного кластера можно гарантировать, что сетевой трафик между сервером API и пулами узлов остается только в частной сети.

Плоскость управления или сервер API находятся в подписке Azure, управляемой Azure Kubernetes Service (AKS). Кластер или пул узлов клиента находится в подписке клиента. Сервер и кластер или пул узлов могут взаимодействовать друг с другом через [службу частной связи Azure][private-link-service] в виртуальной сети сервера API и закрытую конечную точку, доступную в подсети кластера AKS клиента.

> [!IMPORTANT]
> Функции предварительной версии AKS являются самостоятельными и предоставляются на основе согласия. Предварительные версии предоставляются *как есть* и *доступны* и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. Поэтому функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="prerequisites"></a>Технические условия

* Azure CLI версия 2.0.77 или более поздняя и версия расширения Azure CLI AKS Preview 0.4.18

## <a name="currently-supported-regions"></a>Поддерживаемые в настоящее время регионы
* Западная часть США
* Западная часть США 2
* Восточная часть США 2
* Центральная Канада
* Северная Европа
* Западная Европа
* Восточная Австралия

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

## <a name="connect-to-the-private-cluster"></a>Подключение к частному кластеру
Конечная точка сервера API не имеет общедоступного IP-адреса. Следовательно, необходимо создать виртуальную машину Azure в виртуальной сети и подключиться к серверу API. Для этого выполните следующее.

1. Получите учетные данные для подключения к кластеру.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Выполните одно из приведенных ниже действий.
   * Создайте виртуальную машину в той же виртуальной сети, что и кластер AKS.  
   * Создание виртуальной машины в другой виртуальной сети и одноранговая виртуальная сеть с виртуальной сетью кластера AKS.

     Если вы создаете виртуальную машину в другой виртуальной сети, настройте связь между этой виртуальной сетью и частной зоной DNS. Для этого:
    
     а. Перейдите в группу ресурсов MC_ * в портал Azure.  
     b. Выберите частную зону DNS.   
     c. На левой панели выберите ссылку **Виртуальная сеть** .  
     d. Создайте новую ссылку, чтобы добавить виртуальную сеть виртуальной машины в частную зону DNS. Ссылка на зону DNS займет несколько минут.  
     д) Вернитесь к группе ресурсов MC_ * в портал Azure.  
     е) В области справа выберите виртуальную сеть. Имя виртуальной сети указано в формате *AKS-vnet-\** .  
     ж. В левой области выберите **пирингs (пиринга**).  
     h. Выберите **Добавить**, добавьте виртуальную сеть виртуальной машины, а затем создайте пиринг.  
     i. Перейдите в виртуальную сеть с виртуальной машиной, выберите **пиринг**, выберите виртуальную сеть AKS, а затем создайте пиринг. Если диапазоны адресов в виртуальной сети AKS и конфликт виртуальной сети виртуальной машины, пиринг завершится сбоем. Дополнительные сведения см. в разделе [пиринг виртуальных сетей][virtual-network-peering].

1. Доступ к виртуальной машине через Secure Shell (SSH).
1. Установите средство Kubectl и выполните команды Kubectl.


## <a name="dependencies"></a>Зависимости  
* Служба Private Link поддерживается только для стандартных Azure Load Balancer. Базовый Azure Load Balancer не поддерживается.  
* Чтобы использовать настраиваемый DNS-сервер, разверните сервер AD с DNS для пересылки в этот IP-168.63.129.16.

## <a name="limitations"></a>Ограничения 
* Зоны доступности в настоящее время поддерживаются только для регионов "Восточная часть США 2" и "Западная часть США 2".
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
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

