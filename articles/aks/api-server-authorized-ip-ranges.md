---
title: IP-диапазоны авторизации сервера API в службе Kubernetes Azure (AKS)
description: Узнайте, как защитить кластер с помощью диапазона IP-адресов для доступа к серверу API в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 487940bfb5d6e7c5eebf99f804f57c3e17709377
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276486"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Предварительный просмотр — безопасный доступ к серверу API с помощью допустимых диапазонов IP-адресов в службе Kubernetes Azure (AKS)

В Kubernetes сервер API получает запросы на выполнение действий в кластере, например для создания ресурсов или масштабирования количества узлов. Сервер API является основным способом взаимодействия с кластером и управления им. Чтобы повысить безопасность кластера и сократить число атак, сервер API должен быть доступен только из ограниченного набора диапазонов IP-адресов.

В этой статье показано, как использовать диапазоны полномочных IP-адресов сервера API для ограничения запросов к плоскости управления. Эта функция в настоящее время находится на стадии предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения об отсутствии см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что вы работаете с кластерами, использующими [кубенет] [кубенет].  С помощью [сетевого интерфейса контейнера Azure (CNI)] [CNI-Networking], основанного на кластерах на основе, у вас не будет необходимой таблицы маршрутов для защиты доступа.  Таблицу маршрутов потребуется создать вручную.  Дополнительные сведения см. в разделе [Управление таблицами маршрутов](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Допустимые диапазоны IP-адресов сервера API работают только для создаваемых кластеров AKS. В этой статье показано, как создать кластер AKS с помощью Azure CLI.

Требуется Azure CLI версии 2.0.61 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Чтобы настроить IP-диапазоны для авторизации серверов API, требуется расширение CLI *AKS-Preview* версии 0.4.1 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Регистрация флага функции для подписки

Чтобы использовать диапазоны IP-адресов, разрешенные сервером API, сначала включите в подписке флаг функции. Чтобы зарегистрировать флаг функции *аписерверсекуритипревиев* , используйте команду [AZ Feature Register][az-feature-register] , как показано в следующем примере:

> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать значения по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Проверить состояние регистрации можно с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Создание исходящего шлюза для правил брандмауэра

Чтобы обеспечить надежную связь узлов в кластере с сервером API при включении диапазонов разрешенных IP-адресов в следующем разделе, создайте брандмауэр Azure для использования в качестве исходящего шлюза. После этого IP-адрес брандмауэра Azure добавляется в список IP-адресов полномочных серверов API в следующем разделе.

> [!WARNING]
> Использование брандмауэра Azure может повлечь за собой значительные затраты в течение ежемесячного цикла выставления счетов. Требование использования брандмауэра Azure должно быть необходимым только в этом начальном периоде предварительной версии. Дополнительные сведения и планирование затрат см. в разделе [цены на брандмауэры Azure][azure-firewall-costs].
>
> Кроме того, если в кластере используется [балансировщик нагрузки "Стандартный][standard-sku-lb]", не нужно настраивать брандмауэр Azure в качестве исходящего шлюза. Используйте команду [AZ Network public-IP List][az-network-public-ip-list] и укажите группу ресурсов кластера AKS, которая обычно начинается с *MC_* . Отобразится общедоступный IP-адрес кластера, который можно список разрешений. Пример:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Сначала получите имя группы ресурсов *MC_* для кластера AKS и виртуальной сети. Затем создайте подсеть с помощью команды [AZ Network vnet подсети Create][az-network-vnet-subnet-create] . В следующем примере создается подсеть с именем *азурефиреваллсубнет* с диапазоном CIDR *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Чтобы создать брандмауэр Azure, установите расширение CLI для *Azure-Firewall* с помощью команды [AZ Extension Add][az-extension-add] . Затем создайте брандмауэр с помощью команды [AZ Network Firewall Create][az-network-firewall-create] . В следующем примере создается брандмауэр Azure с именем *мязурефиревалл*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Брандмауэру Azure назначается общедоступный IP-адрес, через который исходящий трафик проходит через. Создайте общедоступный адрес с помощью команды [AZ Network public-IP Create][az-network-public-ip-create] , а затем создайте IP-конфигурацию в брандмауэре, выполнив командлет [AZ Network Firewall IP-config][az-network-firewall-ip-config-create] , который применяет общедоступный IP:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Теперь создайте сетевое правило брандмауэра Azure, чтобы *Разрешить* весь трафик *TCP* с помощью команды [AZ Network Firewall Network-Rule Create][az-network-firewall-network-rule-create] . В следующем примере создается сетевое правило с именем *алловткпаутбаунд* для трафика с любым адресом источника или назначения.

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Чтобы связать брандмауэр Azure с сетевым маршрутом, получите существующие сведения о таблице маршрутов, внутренний IP-адрес брандмауэра Azure, а затем IP-адрес сервера API. Эти IP-адреса указаны в следующем разделе, чтобы управлять маршрутизацией трафика для обмена данными с кластером.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Наконец, создайте маршрут в существующей таблице сетевого маршрута AKS, выполнив команду [AZ Network Route-Table Route Create][az-network-route-table-route-create] , которая разрешает трафику использовать устройство брандмауэра Azure для взаимодействия с сервером API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Запишите общедоступный IP-адрес устройства брандмауэра Azure. Этот адрес добавляется в список диапазонов IP-адресов, разрешенных сервером API, в следующем разделе.

## <a name="enable-authorized-ip-ranges"></a>Включить диапазоны разрешенных IP-адресов

Чтобы включить диапазоны разрешенных IP-адресов для сервера API, необходимо предоставить список разрешенных диапазонов. При указании диапазона CIDR начинается с первого IP-адреса в диапазоне. Например, *137.117.106.90/29* является допустимым диапазоном, но убедитесь, что вы указали первый IP-адрес в диапазоне, например *137.117.106.88/29*.

Выполните команду [AZ AKS Update][az-aks-update] и укажите разрешения *--API-Server-Allowed-IP-ranges* . Эти диапазоны IP-адресов обычно являются диапазонами адресов, используемыми локальными сетями. Добавьте общедоступный IP-адрес собственного брандмауэра Azure, полученного на предыдущем шаге, например *20.42.25.196/32*.

В следующем примере включается авторизация IP-адресов сервера API в кластере с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*. Диапазоны IP-адресов для авторизации: *20.42.25.196/32* (общедоступный IP-адрес брандмауэра Azure), затем *172.0.0.0/16* и *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Обновление или отключение диапазонов IP-адресов с правом доступа

Чтобы обновить или отключить допустимые диапазоны IP-адресов, снова выполните команду [AZ AKS Update][az-aks-update] . Укажите обновленный диапазон CIDR, который вы хотите разрешить, или укажите пустой диапазон, чтобы отключить разрешенные IP-диапазоны сервера API, как показано в следующем примере:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы включили IP-диапазоны разрешенных серверов API. Этот подход является одной из частей того, как можно запустить защищенный кластер AKS.

Дополнительные сведения см. в статьях [Основные понятия безопасности для приложений и кластеров в AKS][concepts-security] и рекомендации [по обеспечению безопасности и обновлениям кластера в AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
