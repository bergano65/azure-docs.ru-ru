---
title: Частный кластер службы Kubernetes Azure
description: Узнайте, как создать частный кластер службы Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480090"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Общедоступная Предварительная версия — частный кластер службы Kubernetes Azure

В частном кластере плоскость управления или сервер API будут иметь внутренние IP-адреса, определенные в [адресные пространства RFC1918](https://tools.ietf.org/html/rfc1918).  С помощью частного кластера можно гарантировать, что сетевой трафик между сервером API и пулами узлов остается только в частной сети.

Обмен данными между плоскостью управления и сервером API, который находится в подписке Azure, управляемой AKS, и пулом кластеров и узлов, который находится в клиентской подписке, может взаимодействовать друг с другом через [службу Private Link][private-link-service] в виртуальной сети сервера API и закрытую конечную точку в подсети кластера AKS клиента.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="before-you-begin"></a>Перед началом работы

* Требуется Azure CLI версии 2.0.77 или более поздней, а также расширение AKS-Preview 0.4.18

## <a name="current-supported-regions"></a>Текущие поддерживаемые регионы
* Западная часть США
* Западная часть США 2
* Восточная часть США 2
* Центральная Канада
* Северная Европа
* Западная Европа
* Восточная Австралия

## <a name="install-latest-aks-cli-preview-extension"></a>Установка последнего расширения AKS CLI Preview

Для использования частных кластеров требуется расширение CLI *AKS-Preview* версии 0.4.18 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать значения по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Для отображения *зарегистрированного*состояния может потребоваться несколько минут. Проверить состояние регистрации можно с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

После регистрации состояния обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Создание частного кластера AKS

#### <a name="default-basic-networking"></a>Основные сетевые подключения по умолчанию 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Где--Enable-частный-Cluster является обязательным флагом для частного кластера 

#### <a name="advanced-networking"></a>Расширенные сетевые подключения  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Где--Enable-частный-Cluster является обязательным флагом для частного кластера 

## <a name="steps-to-connect-to-the-private-cluster"></a>Действия для подключения к частному кластеру
Конечная точка сервера API не имеет общедоступного IP-адреса. Следовательно, пользователям потребуется создать виртуальную машину Azure в виртуальной сети и подключиться к серверу API. Действия в

* Получение учетных данных для подключения к кластеру

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Создайте виртуальную машину в той же виртуальной сети, где находится кластер AKS, или создайте виртуальную машину в другой виртуальной сети и одноранговую сеть с ВИРТУАЛЬНОЙ сетью AKS.
* При создании виртуальной машины в другой виртуальной сети необходимо настроить связь между этой ВИРТУАЛЬНОЙ сетью и зоной Частная зона DNS.
    * Перейдите в группу ресурсов MC_ * на портале. 
    * Щелкните зону Частная зона DNS 
    * в левой области выберите ссылку на виртуальную сеть.
    * Создайте новую ссылку, чтобы добавить виртуальную сеть виртуальной машины в зону Частная зона DNS *(это займет несколько минут, чтобы ссылка зоны DNS стала доступной)* .
* Подключение SSH к виртуальной машине
* Установка средства Kubectl и выполнение команд Kubectl

## <a name="dependencies"></a>Зависимости  
* Только стандартная БАЛАНСИРОВКа нагрузки — без поддержки базовой подсистемы балансировки загрузки  

## <a name="limitations"></a>Ограничения 
* Те же [ограничения службы частной связи Azure][private-link-service] применяются к частным кластерам, а частные конечные точки Azure и конечные точки службы виртуальной сети в настоящее время не поддерживаются в одной виртуальной сети.
* Нет поддержки виртуальных узлов в частном кластере для прокрутки частных экземпляров ACI в частной виртуальной сети Azure.
* Без поддержки интеграции Azure DevOps из Box с частными кластерами
* Если пользователям необходимо включить запись контроля доступа для работы с частным AKS, виртуальная сеть записи контроля доступа должна быть соединена с ВИРТУАЛЬНОЙ сетью кластера агента.
* Нет текущей поддержки для Azure Dev Spaces
* Нет поддержки преобразования существующих кластеров AKS в частные кластеры  
* Удаление или изменение частной конечной точки в подсети клиента приведет к прекращению работы кластера 
* Azure Monitor для динамических данных контейнеров в настоящее время не поддерживается
* В настоящее время не поддерживается поддержка DNS


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
