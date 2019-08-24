---
title: Развертывание приложения двойного стека IPv6 в виртуальной сети Azure с помощью интерфейса командной строки
titlesuffix: Azure Virtual Network
description: В этой статье показано, как развернуть приложение с двумя стеками IPv6 в виртуальной сети Azure с помощью Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: f67494b229a14b66b593950903184e54e4a8ab8c
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013702"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Развертывание приложения с двумя стеками IPv6 в виртуальной сети Azure с помощью интерфейса командной строки (Предварительная версия)

В этой статье показано, как развернуть приложение с двойным стеком (IPv4 + IPv6) с помощью Load Balancer (цен. категория "Стандартный") в Azure, которое включает в себя виртуальную сеть с двумя стеками с двойной подсетью, Load Balancer (цен. категория "Стандартный") с двумя интерфейсными конфигурациями (IPv4 + IPv6), ВМ с Сетевые карты с двумя IP-конфигурациями, двумя правилами группы безопасности сети и двумя общедоступными IP.

> [!Important]
> В настоящее время два стека IPv6 для виртуальной сети Azure находятся в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, в этом кратком руководстве необходимо использовать Azure CLI версии 2.0.49 или более поздней. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Предварительные требования
Чтобы использовать функцию IPv6 для виртуальной сети Azure, необходимо настроить подписку с помощью Azure PowerShell следующим образом.

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Регистрация функции занимает до 30 минут. Состояние регистрации можно проверить, выполнив следующую команду Azure CLI:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
После регистрации выполните следующую команду:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Создать группу ресурсов

Прежде чем можно будет создать виртуальную сеть с двумя стеками, необходимо создать группу ресурсов с помощью команды [AZ Group Create](/cli/azure/group). В следующем примере создается группа ресурсов с именем *миргдуалстакк* в расположении *eastus* :

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Создание общедоступных IP-адресов IPv4 и IPv6 для балансировщика нагрузки
Для доступа к конечным точкам IPv4 и IPv6 в Интернете требуются общедоступные IP-адреса IPv4 и IPv6 для балансировщика нагрузки. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip). В следующем примере создается общедоступный IP-адрес IPv4 и IPv6 с именем *dsPublicIP_v4* и *dsPublicIP_v6* в группе ресурсов *миргдуалстакк* :

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Создание общедоступных IP-адресов для виртуальных машин

Для удаленного доступа к виртуальным машинам в Интернете требуются общедоступные IP-адреса IPv4 для виртуальных машин. Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>Создание Load Balancer уровня "Стандартный"

В этом разделе вы настроите сдвоенный интерфейсный IP-адрес (IPv4 и IPv6) и пул внутренних адресов для балансировщика нагрузки, а затем создадите Load Balancer (цен. категория "Стандартный").

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте Load Balancer (цен. категория "Стандартный") с помощью команды [AZ Network фунтов Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) с именем **дслб** , которая включает интерфейсный пул с именем **dsLbFrontEnd_v4**, внутренний пул с именем **dsLbBackEndPool_v4** , связанный с общедоступным IP-адресом **IPv4. dsPublicIP_v4** , созданный на предыдущем шаге. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Создание внешнего интерфейса IPv6

Создайте интерфейсный IP-адрес IPV6 с помощью команды [AZ Network фунтов интерфейсного интерфейса-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). В следующем примере создается внешняя IP-конфигурация с именем *dsLbFrontEnd_v6* и прикрепляется адрес *dsPublicIP_v6* :

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Настройка пула адресов IPv6 для серверной части

Создайте пулы внутренних адресов IPv6 с помощью команды [AZ Network фунтов Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). В следующем примере создается пул внутренних адресов с именем *dsLbBackEndPool_v6* для включения виртуальных машин с КОНФИГУРАЦИЯМИ сетевых адаптеров IPv6:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете конфигурацию внешнего IP-адреса для входящего трафика и пул внутренних IP-адресов для приема трафика, а также требуемый порт источника и назначения. 

Создайте правило балансировщика нагрузки с помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). В следующем примере создаются правила подсистемы балансировки нагрузки с именами *dsLBrule_v4* и *dsLBrule_v6* , а также баланс трафика по *TCP* -порту *80* в конфигурациях интерфейсов IPv4 и IPv6.

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
Перед развертыванием некоторых виртуальных машин необходимо создать вспомогательные сетевые ресурсы — группы доступности, группу безопасности сети, виртуальную сеть и виртуальные сетевые карты. 
### <a name="create-an-availability-set"></a>Создать группу доступности
Чтобы повысить доступность приложения, разместите виртуальные машины в группе доступности.

Создайте группу доступности с помощью команды [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). В следующем примере создается группа доступности с именем *дсавсет*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети для правил, которые будут управлять входящим и исходящим обменом данными в виртуальной сети.

#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью команды [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) .


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Создание правила группы безопасности сети для входящих и исходящих соединений

Создайте правило группы безопасности сети, разрешающее подключения по протоколу RDP через порт 3389, подключение к Интернету через порт 80 и исходящие подключения с помощью команды [AZ Network NSG правило Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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

Создайте виртуальную сеть с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). В следующем примере создается виртуальная сеть с именем *дсвнет* с подсетями *dsSubNET_v4* и *dsSubNET_v6*:

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

Создайте виртуальные сетевые карты для каждой виртуальной машины с помощью команды [AZ Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). В следующем примере создается виртуальный сетевой адаптер для каждой виртуальной машины. Каждая сетевая карта имеет две конфигурации IP-адресов (1 Конфигурация IPv4, 1 Конфигурация IPv6). Создайте конфигурацию IPV6 с помощью команды [AZ Network NIC IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Создайте виртуальную машину *dsVM0* следующим образом:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```
Создайте виртуальную машину *dsVM1* следующим образом:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Просмотр двух виртуальных сетей IPv6 с двумя стеками в портал Azure
Виртуальную сеть с двумя стеками IPv6 можно просмотреть в портал Azure следующим образом:
1. На панели поиска портала введите *дсвнет*.
2. Когда в результатах поиска появится пункт **myVirtualNetwork**, выберите его. Откроется страница **обзора** для виртуальной сети с двумя стеками с именем *дсвнет*. В виртуальной сети с двумя стеками показаны две сетевые карты с конфигурациями IPv4 и IPv6, расположенными в подсети с двойным стеком с именем *дссубнет*.

  ![Виртуальная сеть с двумя стеками IPv6 в Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Виртуальная сеть IPv6 для виртуальной сети Azure доступна в портал Azure в режиме только для чтения для этой предварительной версии.


## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы создали Load Balancer (цен. категория "Стандартный") с двойной интерфейсной IP-конфигурацией (IPv4 и IPv6). Вы также создали две виртуальные машины, которые включали сетевые карты с двумя IP-конфигурациями (IPV4 + IPv6), которые были добавлены в пул внутренних подсистем подсистемы балансировки нагрузки. Дополнительные сведения о поддержке IPv6 в виртуальных сетях Azure см. в статье [что такое IPv6 для виртуальной сети Azure?](ipv6-overview.md)