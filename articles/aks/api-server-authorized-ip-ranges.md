---
title: Авторизация сервера API диапазонов IP-адресов в службе Azure Kubernetes (AKS)
description: Узнайте, как для защиты кластера с помощью диапазон IP-адресов для доступа к серверу API в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: d425fc3e037b43321a278fb16781e86dfafef15c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441528"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Предварительная версия — безопасный доступ к серверу API с помощью авторизованных диапазоны IP-адресов в службе Azure Kubernetes (AKS)

В Kubernetes сервер API получает запросы на выполнение действия в кластере, например, чтобы создать ресурсы или масштабировать количество узлов. Сервер API — это централизованный способ взаимодействия с и администрирования кластера. Чтобы повысить уровень безопасности кластера и свести к минимуму атак, сервер API только должен быть доступен из ограниченного набора диапазонов IP-адресов.

В этой статье показано, как использовать диапазоны IP-адресов API сервера авторизации для ограничения запросов для плоскости управления. Эта функция в настоящее время находится на стадии предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS: самообслуживания, согласиться. Они предназначены для сбора отзывов и ошибки нашего сообщества. В предварительной версии эти функции не предназначены для использования в рабочей среде. Функции в общедоступной предварительной версии подпадают под поддержки «оптимальных затрат». Помощь от групп разработчиков AKS Техническая поддержка доступна во время рабочих часов тихоокеанского часового пояса (по тихоокеанскому времени) только. Дополнительные сведения обратитесь в следующие справочные статьи:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="before-you-begin"></a>Перед началом работы

Сервер API авторизованным IP-диапазонов работает только для новых кластеров AKS, создаваемые. В этой статье показано, как создать кластер AKS с помощью Azure CLI.

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если требуется выполнить установку или обновление, см. в разделе [установить Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Чтобы настроить диапазоны IP-адрес сервера авторизации API, вам потребуется *предварительной версии aks* CLI версия расширения 0.4.1 или более поздней версии. Установка *предварительной версии aks* расширение Azure CLI с помощью [добавить расширения az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] команды:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Регистрация флаг компонента для подписки

Чтобы использовать API сервер авторизован диапазоны IP-адресов, сначала включите флаг компонента по вашей подписке. Чтобы зарегистрировать *APIServerSecurityPreview* флаг, используйте [az функция register][az-feature-register] команды, как показано в следующем примере:

> [!CAUTION]
> При регистрации компонента для подписки, вы не можете сейчас отменить регистрацию этой функции. После включения некоторые возможности предварительной версии, можно использовать значения по умолчанию для всех кластеров AKS, затем создается в подписке. Не включайте функции предварительной версии на рабочие подписки. Используйте отдельную подписку для тестирования функции предварительной версии и собирайте отзывы.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Вы можете проверить состояние регистрации с помощью [список функций az][az-feature-list] команды:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновить регистрацию *Microsoft.ContainerService* поставщик ресурсов с помощью [az provider register][az-provider-register] команды:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ограничения

При настройке диапазонов IP-адрес сервера авторизации API, применяются следующие ограничения:

* Сейчас нельзя использовать пробелы разработки Azure как связь с сервером API также заблокирован.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Обзор сервера API-интерфейса авторизованных диапазоны IP-адресов

Сервер Kubernetes API является то, каким образом доступны базовые интерфейсы API Kubernetes. Этот компонент поддерживает взаимодействие со средствами управления, например с `kubectl` или панелью мониторинга Kubernetes. AKS предоставляет главного кластера с одним клиентом, с выделенным сервером API. По умолчанию сервер API назначается общедоступный IP-адрес, и вы сами должны управлять доступом с помощью элементов управления доступом на основе ролей (RBAC).

Для защиты доступа к плоскости управления в противном случае общедоступным AKS / сервера API-интерфейса, можно включить и использовать авторизацию диапазоны IP-адресов. Эти авторизованных диапазоны IP-адресов разрешить только определенные диапазоны IP-адресов для связи с сервером API. Запрос к API сервер с IP-адреса, который не является частью этих авторизованных диапазонов IP-блокируется. Вы должны продолжать использовать RBAC, чтобы авторизовать пользователей и действий, которую они запроса.

Дополнительные сведения о сервере API и другие компоненты, см. в разделе [Kubernetes ключевые понятия для AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Создание кластера AKS

IP-адрес сервера авторизации API диапазоны работают только для новых кластеров в AKS. Невозможно включить авторизованных диапазоны IP-адресов, так как операция создания частью кластера. Если вы попытаетесь включить авторизованных диапазоны IP-адресов как часть кластера создать процесс, узлах кластера может получить доступ к API сервера во время развертывания, как IP-адрес исходящего трафика не определено в этот момент.

Во-первых, создайте кластер с помощью [создать az aks][az-aks-create] команды. В следующем примере создается кластер с одним узлом с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Создание исходящего трафика шлюза для правил брандмауэра

Чтобы узлы в кластере можно надежно обмениваться данными с сервером API при включении авторизованных диапазоны IP-адрес в следующем разделе, создайте брандмауэр, Azure для использования в качестве исходящего трафика шлюза. IP-адрес брандмауэра Azure затем добавляется в список авторизованных API IP-адреса сервера в следующем разделе.

> [!WARNING]
> Использование брандмауэра Azure могут повлечь за собой значительные затраты ежемесячного цикла. Требование использовать брандмауэр Azure необходима только в этот период исходной предварительной версии. Дополнительные сведения и планирование затрат, см. в разделе [брандмауэр Azure цены][azure-firewall-costs].

Во-первых, получите *MC_* имя группы ресурсов в кластере AKS и виртуальной сети. Затем создайте подсеть, используя [Создание подсети виртуальной сети az сети][az-network-vnet-subnet-create] команды. В следующем примере создается подсеть с именем *AzureFirewallSubnet* с диапазоном CIDR *10.200.0.0/16*:

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

Чтобы создать брандмауэр подключения к Azure, установите *брандмауэр azure* CLI с помощью расширения [добавить расширения az][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] команды. В следующем примере создается брандмауэра Azure с именем *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Брандмауэр Azure назначается общедоступный IP-адрес, который проходит исходящий трафик через. Создание общедоступного адреса с помощью [создать az сети public-ip][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] , применяющее общедоступный IP-адрес:

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

Теперь создайте сетевое правило брандмауэра Azure для *Разрешить* все *TCP* трафике с помощью [брандмауэра сети Azure network-rule create][az-network-firewall-network-rule-create] команды. В следующем примере создается правило с именем сети *AllowTCPOutbound* для трафика с помощью любого адреса источника или назначения:

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

Должен быть сопоставлен брандмауэр Azure на сетевом маршруте, получите сведения о существующем маршруте таблицы, внутренний IP-адрес брандмауэра Azure и IP-адрес сервера API-интерфейса. Эти IP-адреса указаны в следующем разделе для управления маршрутизацией трафика для взаимодействия с кластером.

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

Наконец, создайте маршрут в существующих AKS сетевого маршрута таблицы с помощью [создать таблицу маршрутов az маршрут][az-network-route-table-route-create] команды, которое разрешает трафик, использовать устройство брандмауэра Azure для обмена данными с сервером API.

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

Запомните или запишите общедоступный IP-адрес вашего устройства брандмауэра Azure. Этот адрес добавляется в список диапазонов IP-адрес сервера авторизации API в следующем разделе.

## <a name="enable-authorized-ip-ranges"></a>Включить авторизованных диапазоны IP-адресов

Чтобы включить диапазоны IP-адрес сервера авторизации API, укажите список авторизованных диапазоны IP-адресов. При указании диапазона CIDR, начните с первый IP-адрес в диапазоне. Например *137.117.106.90/29* допустимый диапазон, но убедитесь, что вы указать первый IP-адрес в диапазоне, такие как *137.117.106.88/29*.

Используйте [обновление az aks][az-aks-update] команду и укажите *--api-server авторизованных-диапазоны ip адресов* чтобы разрешить. Эти диапазоны IP-адресов обычно диапазоны адресов, используемых в локальной сети. Добавьте общедоступный IP-адрес собственных брандмауэра Azure, полученный на предыдущем шаге, такие как *20.42.25.196/32*.

В следующем примере включается диапазоны IP-адрес сервера авторизации API в кластере с именем *myAKSCluster* в группе ресурсов с именем *myResourceGroup*. Диапазоны IP-адресов для авторизации, *20.42.25.196/32* (брандмауэр Azure общедоступный IP-адрес), затем *172.0.0.10/16* и *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Обновление или отключение авторизованных диапазоны IP-адресов

Чтобы обновить или отключить авторизованных диапазоны IP-адресов, можно повторно использовать [обновление az aks][az-aks-update] команды. Укажите обновленный диапазон CIDR, которые вы хотите разрешить, или указать пустой диапазон для отключения сервера API авторизованным диапазоны IP-адресов, как показано в следующем примере:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы включили диапазоны IP-адрес сервера авторизации API. Этот подход является одной из частей процесса выполнения защищенного кластера AKS.

Дополнительные сведения см. в разделе [концепции безопасности для приложений и кластеров в AKS][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
