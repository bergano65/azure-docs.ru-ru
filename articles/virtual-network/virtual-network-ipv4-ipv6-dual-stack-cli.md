---
title: Развертывание приложения двойной стек IPv6 в виртуальной сети Azure - CLI
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение двойной стек IPv6 в виртуальной сети Azure, с помощью Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131012"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Развертывание приложения двойной стек IPv6 в виртуальной сети Azure - CLI (Предварительная версия)

В этой статье показано, как развернуть приложение в Azure, которая включает в себя двойной стек виртуальной сети с подсетью двойной стек, подсистему балансировки нагрузки с помощью интерфейсные конфигурации двумя (IPv4 + IPv6), виртуальные машины с сетевыми интерфейсами, которые имеют два IP-конфигурацию, двойной стек (IPv4 + IPv6) правила группы безопасности сети с двумя и два общедоступных IP-адресов.

> [!Important]
> Двойной стек протокола IPv6 для виртуальной сети Azure в настоящее время находится в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, в этом кратком руководстве необходимо использовать Azure CLI версии 2.0.49 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Технические условия
Чтобы использовать IPv6 для компонентов виртуальной сети Azure, необходимо настроить подписки с помощью Azure PowerShell следующим образом:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Регистрация функции занимает до 30 минут. Можно проверить состояние регистрации, выполнив следующую команду Azure CLI:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
После регистрации выполните следующую команду:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием виртуальной сети двойного стека, необходимо создать группу ресурсов с помощью [Создание группы az](/cli/azure/group). В следующем примере создается группа ресурсов, с именем *myRGDualStack* в *eastus* расположение:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Создать IPv4 и IPv6 общедоступные IP-адреса для подсистемы балансировки нагрузки
Для доступа к IPv4 и IPv6 конечным точкам в Интернете, требуется общедоступный IP-адресов IPv4 и IPv6 для подсистемы балансировки нагрузки. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip). В следующем примере создается IPv4 и IPv6 общедоступный IP-адрес с именем *dsPublicIP_v4* и *dsPublicIP_v6* в *myRGDualStack* группы ресурсов:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Создание общедоступных IP-адресов для виртуальных машин

Удаленный доступ к виртуальным машинам в Интернете, требуется общедоступный IP-адресов IPv4 для виртуальных машин. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе Настройка двух frontend IP (IPv4 и IPv6) и пула внутренних адресов для балансировщика нагрузки и затем создать основные подсистемы балансировки нагрузки.

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создание основные подсистемы балансировки нагрузки с [создать балансировки нагрузки сети az](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) с именем **dsLB** , включает в себя с интерфейсным пулом **dsLbFrontEnd_v4**, серверный пул с именем  **dsLbBackEndPool_v4** связан общедоступный IP-адрес IPv4 **dsPublicIP_v4** , созданный на предыдущем шаге. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Создание внешнего интерфейса IPv6

Создание IP-адрес внешнего интерфейса IPV6 с [создать az lb frontend-IP-адрес сети](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). В следующем примере создается внешняя IP-конфигурация с именем *dsLbFrontEnd_v6* и присоединяет *dsPublicIP_v6* адрес:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Настройка пула внутренних адресов IPv6

Создание использования IPv6-адреса внутренних пулов с [az сети lb-пула адресов создайте](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). В следующем примере создается пул адресов серверной части с именем *dsLbBackEndPool_v6* включать виртуальные машины с конфигурациями IPv6 сетевого Адаптера:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете конфигурацию внешнего IP-адреса для входящего трафика и пул внутренних IP-адресов для приема трафика, а также требуемый порт источника и назначения. 

Создайте правило балансировщика нагрузки с помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). В следующем примере создается правила подсистемы балансировки нагрузки с именем *dsLBrule_v4* и *dsLBrule_v6* и распределяет трафик на *TCP* порт *80* для IPv4 и IPv6 интерфейса IP-конфигурации:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Перед развертыванием некоторые виртуальные машины, поддерживающие сетевые ресурсы — необходимо создать группу доступности, группа безопасности сети, виртуальной сети и виртуальных сетевых адаптеров. 
### <a name="create-an-availability-set"></a>"Создать группу доступности"
Чтобы повысить доступность приложения, поместите виртуальные машины в группе доступности.

Создайте группу доступности с помощью команды [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). В следующем примере создается группа доступности *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети для правил, определяющих входящие и исходящие подключения в виртуальной сети.

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью [Создание групп безопасности сети az](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Создайте правило группы безопасности сети для входящих и исходящих подключений

Создайте правило группы безопасности сети для подключений по протоколу RDP через порт 3389, подключение к Интернету через порт 80, а также для исходящих подключений с помощью [Azure network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Создайте виртуальную сеть с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). В следующем примере создается виртуальная сеть с именем *dsVNET* с подсетями *dsSubNET_v4* и *dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Создание сетевых адаптеров

Создание виртуальных сетевых адаптеров для каждой виртуальной Машины с [создать сетевую карту сети az](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). В следующем примере создается виртуальный сетевой Адаптер для каждой виртуальной Машины. Каждый сетевой Адаптер имеет две IP-конфигурации (1 IPv4 config, 1 IPv6 config). Создайте конфигурацию IPV6 с [создать ip конфигурации сетевого адаптера сети. az](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет: 

Создание виртуальной машины *dsVM0* следующим образом:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Создание виртуальной машины *dsVM1* следующим образом:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Представление IPv6 двойной стек виртуальной сети на портале Azure
Двойной стек виртуальной сети IPv6 на портале Azure можно просмотреть следующим образом:
1. В панели поиска портала, введите *dsVnet*.
2. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его. Это откроет **Обзор** страницы двойной стек виртуальной сети с именем *dsVnet*. Двойной стек виртуальной сети показано два сетевых адаптера с конфигурациями IPv4 и IPv6, расположенный в подсети двойной стек с именем *dsSubnet*.

  ![IPv6 двойной стек виртуальной сети в Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Протокол IPv6 для виртуальной сети Azure доступен на портале Azure в этой предварительной версии только для чтения.


## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы создали основные подсистемы балансировки нагрузки с двумя внешнюю конфигурацию IP-адрес (IPv4 и IPv6). Вы также создали две виртуальные машины, которые включены сетевые карты с двумя IP-конфигурации (IPV4 + IPv6), которые были добавлены в пул серверной части подсистемы балансировки нагрузки. Дополнительные сведения о поддержке IPv6 в виртуальных сетях Azure см. в разделе [Какова IPv6 для виртуальной сети Azure?](ipv6-overview.md)